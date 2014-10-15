A quoi sert l'évènement kernel.terminate ? Regardons du côté de la documentation :

> "To perform some "heavy" action after the response has been streamed to the user".

Une question que vous vous posez surement si vous n'avez pas eu l'occasion de travailler avec cet évènement : &#8220;Quand est-ce que je peux utiliser l'évènement "kernel.terminate" pour effectuer mes traitements ?&#8221;. La réponse en image :

[<img class="aligncenter size-full wp-image-2837" alt="2013 07 18 1331 Utilisation de lévènement kernel.terminate sous Symfony2" src="http://www.elao.com/blog/wp-content/uploads/2013/07/2013-07-18_1331.png" width="488" height="522" title="Utilisation de lévènement kernel.terminate sous Symfony2" />][1]

Concrètement, vous pouvez quasiment tout faire si vous utilisez cet évènement. A une chose près : votre traitement ne doit pas altérer la réponse. Pourquoi ? Parce qu&#8217;il est déclenché après que la réponse soit envoyé au client. Il n&#8217;y à donc plus moyen d&#8217;y rajouter des informations ou d&#8217;en altérer son contenu dans le but de l&#8217;envoyer au client. (Attention kernel.terminate a été rajouté en Symfony2.1, donc si vous êtes encore en 2.0, vous pouvez oublier).

<noscript>
  <pre><code class="language-php php">// ...
$response = $kernel-&gt;handle($request);
$response-&gt;send();
$kernel-&gt;terminate($request, $response);
</code></pre>
</noscript>

Alors finalement comment le mettre en place ? Voilà un exemple de la classe *GuzzleExceptionListener* qui écoute 2 évènements.

<noscript>
  <pre><code class="language-php php">&lt;?php

namespace Tristanbes\ElophantBundle\EventListener;

use Guzzle\Http\Exception\BadResponseException;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\HttpKernel\Event\GetResponseForExceptionEvent;

use Tristanbes\ElophantBundle\Manager\StatsManager;

/**
 * Class GuzzleExceptionListener
 */
class GuzzleExceptionListener
{
    private $statsManager;
    private $fail = false;

    /**
     * Constructor
     *
     * @param StatsManager $manager The stats Manager
     */
    public function __construct(StatsManager $manager)
    {
        $this-&gt;statsManager = $manager;
    }

    public function onKernelException(GetResponseForExceptionEvent $event)
    {
        $exception = $event-&gt;getException();

        if ($exception instanceof BadResponseException) {
            $this-&gt;fail = true;
            $response   = new JsonResponse(array('success' =&gt; false));
            $event-&gt;setResponse($response);
        }
    }

    public function onKernelTerminate()
    {
        if (false === $this-&gt;fail) {
            return;
        }

        $this-&gt;statsManager-&gt;addFail();
    }
}
</code></pre>
  
  <pre><code class="language-xml xml">&lt;service id="tristanbes_elophant.guzzle_exception_eventlistener" class="%tristanbes_elophant.guzzle.exception.class%"&gt;
    &lt;tag name="kernel.event_listener" event="kernel.exception" method="onKernelException" /&gt;
    &lt;tag name="kernel.event_listener" event="kernel.terminate" method="onKernelTerminate" /&gt;
    &lt;argument type="service" id="tristanbes_elophant.stats.manager" /&gt;
&lt;/service&gt;</code></pre>
</noscript>

Explications :

Le premier évènement écouté est, *kernel.exception. *Ce dernier, lorsqu&#8217;il sera déclenché, appellera la méthode *onKernelException* de la classe *GuzzleExceptionListener* introspectera la classe de l&#8217;exception et regardera si celle ci provient d&#8217;une erreur renvoyée par Guzzle à la suite d&#8217;un appel à un WebService.

Si c&#8217;est le cas, elle modifiera un attribut nommé $fail et renverra une réponse en *json au client.*

Une fois cette réponse envoyée, Symfony va [via la méthode $kernel->terminate()]; déclencher l&#8217;évènement kernel.terminate. Ce qui tombe plutôt bien car notre classe écoute cet évènement aussi. Si jamais $fail renvoie true, le service StatsManager s&#8217;occupera d&#8217;incrémenter une valeur en base de données (afin de savoir combien de requêtes ont échouées par jour).

Ici, on est loin de gagner des secondes sur la réponse, en effet, incrémenter une valeur en base de données est loin d&#8217;être une opération &#8220;lourde&#8221;, alors pourquoi passer par kernel.terminate ? Parce qu&#8217;on peut, alors pourquoi se priver même pour de la &#8220;micro&#8221; optim ?

Attention cependant à bien débugger votre code, car vous ne verrez aucun output étant donné que la réponse est déjà envoyée.

A savoir que vous pouvez aussi ajoutez un listener directement depuis le dispatcher de Symfony2. Voilà un example avec l&#8217;utilisation d&#8217;une closure :

<noscript>
  <pre><code class="language-php php">&lt;?php

namespace Tristanbes\ElophantBundle\EventListener;

use Guzzle\Http\Exception\BadResponseException;
use Symfony\Component\HttpFoundation\JsonResponse;
use Symfony\Component\EventDispatcher\EventDispatcher;
use Symfony\Component\HttpKernel\Event\GetResponseForExceptionEvent;

use Tristanbes\ElophantBundle\Manager\StatsManager;

/**
 * Class GuzzleExceptionListener
 */
class GuzzleExceptionListener
{
    private $statsManager;
    private $dispatcher;
    private $fail = false;

    /**
     * Constructor
     *
     * @param StatsManager $manager The stats Manager
     * @param 
     */
    public function __construct(StatsManager $manager, EventDispatcher $dispatcher)
    {
        $this-&gt;statsManager = $manager;
        $this-&gt;dispatcher   = $dispatcher;
    }

    public function onKernelException(GetResponseForExceptionEvent $event)
    {
        $exception = $event-&gt;getException();
        $statsManager = $this-&gt;statsManager;

        if ($exception instanceof BadResponseException) {
            
            $this-&gt;dispatcher-&gt;addListener('kernel.terminate', function (Event $event) use ($statsManager) {
                $statsManager-&gt;addFail();
            });
        }
    }
}
</code></pre>
  
  <pre><code class="language-xml xml">&lt;service id="tristanbes_elophant.guzzle_exception_eventlistener" class="%tristanbes_elophant.guzzle.exception.class%"&gt;
    &lt;tag name="kernel.event_listener" event="kernel.exception" method="onKernelException" /&gt;
    &lt;argument type="service" id="tristanbes_elophant.stats.manager" /&gt;
    &lt;argument type="service" id="event_dispatcher" /&gt;
&lt;/service&gt;</code></pre>
</noscript>

 [1]: http://www.elao.com/blog/wp-content/uploads/2013/07/2013-07-18_1331.png

*This article is the last part of[ Install Stats.d / Graphite on a debian server in order to monitor a Symfony2 application (1/2)][1].*

Last week, we saw that [StatsDClientBundle][2] provides great metrics for your application,. But what if I want to monitor my own things in my application ? During the last part of this tutorial, we will see** how to monitor our own application events**. I'll assume you did the first part of the tutorial.

In my personnal application (named Seek Team), gamers can purchase a premium account in order to unlock additional features on the website.

<a href="http://www.elao.com/blog/linux/install-stats-d-graphite-on-a-debian-server-to-monitor-a-symfony2-application-12.html/attachment/users" rel="attachment wp-att-2482"><img class="size-thumbnail wp-image-2482 alignleft" title="graphite-users-symfony2" src="http://www.elao.com/blog/wp-content/uploads/2012/11/users-150x150.png" alt="users 150x150 Monitor your Symfony2 application via Stats.d and Graphite (2/2)" width="150" height="150" /></a>
I wanted to display on my graphite dashboard, which looked like the screen on the left, the statistics on how many premium accounts were ordered, cancelled, activated or finished.

So here's what I did :





<noscript>
  <pre><code class="language-php php"><?php

namespace SeekTeam\PremiumBundle\Controller;

use SeekTeam\HomeBundle\Controller\Controller;
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Route;
use Sensio\Bundle\FrameworkExtraBundle\Configuration\Template;
use JMS\SecurityExtraBundle\Annotation\Secure;
use Symfony\Component\EventDispatcher\EventDispatcher;
use Symfony\Component\EventDispatcher\GenericEvent;

use SeekTeam\PremiumBundle\Entity\Premium\Order;
use SeekTeam\PremiumBundle\Event\Events;

/**
 * Default Controller of the PremiumBundle
 */
class DefaultController extends Controller
{
    /**
     * @Route("/{_locale}/payment/{id}/cancel", name="payment_cancel", requirements={"_locale" = "en|fr"})
     * @Template()
     * @Secure(roles="ROLE_USER")
     */
    public function cancelAction(Order $order)
    {
        $order->setStatus(Order::STATUS_CANCEL);

        $this->getEntityManager()->persist($order);
        $this->getEntityManager()->flush($order);

        $this->get('event_dispatcher')->dispatch(Events::PREMIUM_CANCEL, new GenericEvent($order));

        $this->setFlash('error', 'premium.order.cancel');

        return $this->redirect($this->generateUrl('user_profile') . '#premium');
    }

    /**
     * @Route("/{_locale}/payment/{id}/complete", name="payment_complete", requirements={"_locale" = "en|fr"})
     * @Template()
     * @Secure(roles="ROLE_USER")
     */
    public function completeAction(Order $order)
    {
        // Lot of business logic I removed for the tutorial purpose
        $order->setStatus(Order::STATUS_VALID);

        $this->getEntityManager()->persist($order);
        $this->getEntityManager()->flush($order);

        $this->getRepository('SeekTeamPremiumBundle:Premium')->updatePremium($order);

        $this->get('event_dispatcher')->dispatch(Events::PREMIUM_SUCCESS, new GenericEvent($order));

        $this->setFlash('success', 'premium.order.success');

        return $this->redirect($this->generateUrl('homepage'));
    }

    /**
     * Returns the EntityManger
     *
     * @return EntityManager
     */
    protected function getEntityManager()
    {
        return $this->getDoctrine()->getEntityManager();
    }
}
</code></pre>
</noscript>

<noscript>
  <pre><code class="language-php php"><?php

namespace SeekTeam\PremiumBundle\Event;

final class Events
{
    const PREMIUM_START   = 'gamercertified.premium.start';
    const PREMIUM_SUCCESS = 'gamercertified.premium.success';
    const PREMIUM_CANCEL  = 'gamercertified.premium.cancel';
    const PREMIUM_ERROR   = 'gamercertified.premium.error';
}
</code></pre>
</noscript>

<p style="text-align: left;">
  The following code is pretty simple. In the Controller functions, we dispatch 2 events named <em>Events::PREMIUM_CANCEL </em>and <em>Events::PREMIUM_SUCCESS</em> using the <a href="http://symfony.com/doc/current/components/event_dispatcher/introduction.html" target="_blank">EventDispatcher</a> and a <a href="http://symfony.com/doc/master/components/event_dispatcher/generic_event.html" target="_blank">GenericEvent</a> whether the payment was complete or not.
</p>

<p style="text-align: left;">
  Next thing to do, is to create & plug our listener and tell him to listen to those events.
</p>

<noscript>
  <pre><code class="language-php php"><?php

namespace SeekTeam\PremiumBundle\Event\Listener;

use Symfony\Component\EventDispatcher\EventSubscriberInterface;
use Symfony\Component\EventDispatcher\GenericEvent;

use SeekTeam\PremiumBundle\Event\Events;

class StatsListener implements EventSubscriberInterface
{
    protected $statsdFactory;
    protected $statsdClient;

    public function __construct($statsdFactory, $statsdClient)
    {
        $this->statsdFactory = $statsdFactory;
        $this->statsdClient  = $statsdClient;
    }

    public static function getSubscribedEvents()
    {
        return array(
            Events::PREMIUM_SUCCESS => 'onPremiumSuccess',
            Events::PREMIUM_START   => 'onPremiumStart',
            Events::PREMIUM_CANCEL  => 'onPremiumCancel',
            Events::PREMIUM_ERROR   => 'onPremiumError',
        );
    }

    public function onPremiumSuccess(GenericEvent $event)
    {
        $data = $this->statsdFactory->createStatsDataIncrement('premium.success');
        $this->statsdClient->send($data);
    }

    public function onPremiumCancel(GenericEvent $event)
    {
        $data = $this->statsdFactory->createStatsDataIncrement('premium.cancel');
        $this->statsdClient->send($data);
    }

    public function onPremiumStart(GenericEvent $event)
    {
        $data = $this->statsdFactory->createStatsDataIncrement('premium.start');
        $this->statsdClient->send($data);
    }

    public function onPremiumError(GenericEvent $event)
    {
        $data = $this->statsdFactory->createStatsDataIncrement('premium.error');
        $this->statsdClient->send($data);
    }
}
</code></pre>
</noscript>

You have noticed in the constructor the presence of 2 services that we need in order to send to Stats.d the data. Each time you'll add a "." to your stats category (in the example I used *premium.success*, it'll create a subfolder in your graphite dashboard).

So let's inject them via the <a href="http://symfony.com/doc/2.0/components/dependency_injection/introduction.html" target="_blank">DI</a>.

<noscript>
  <pre><code class="language-xml xml"> <service id="seek_team_premium.contact.listener" class="SeekTeam\PremiumBundle\Event\Listener\StatsListener">
     <tag name="kernel.event_subscriber" />
     <argument type="service" id="liuggio_stats_d_client.factory" />
     <argument type="service" id="liuggio_stats_d_client.service" />
</service></code></pre>
</noscript>

## And it's done, if you go on your graphite dashboard, you can now see the new category called "premium"<a href="http://www.elao.com/blog/symfony-2/monitor-your-symfony2-application-via-stats-d-and-graphite-22.html/attachment/premium_stat" rel="attachment wp-att-2562"><img class="aligncenter size-medium wp-image-2562" title="premium_stat" src="http://www.elao.com/blog/wp-content/uploads/2012/11/premium_stat-256x300.png" alt="premium stat 256x300 Monitor your Symfony2 application via Stats.d and Graphite (2/2)" width="256" height="300" /></a>





 [1]: http://www.elao.com/blog/linux/install-stats-d-graphite-on-a-debian-server-to-monitor-a-symfony2-application-12.html "Install Stats.d / Graphite on a debian server in order to monitor a Symfony2 application (1/2)"
 [2]: https://github.com/liuggio/StatsDClientBundle
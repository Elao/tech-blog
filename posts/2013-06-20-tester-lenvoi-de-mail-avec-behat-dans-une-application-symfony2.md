Dans la dernière application que je suis en train de développer, un e-mail est envoyé au moment de l'inscription d'un utilisateur, il est donc logique d'ajouter un scénario Behat pour tester cette fonctionnalité. J'entends souvent qu'il n'est pas possible de tester fonctionnellement l'envoi d'email, je vous propose donc un exemple de scénario pour vérifier l'envoi du mail lors de l'inscription de l'utilisateur avec Behat.

```
@javascript
Scenario: User registration
  Given I am on the homepage
    And I open the "registration" modal
  When I fill in the following:
    | username | admin@example.com |
    | password | password          |
    And I press "Register"
  Then the "registration" mail should be sent to "admin@example.com"
```

**Prenons l'étape qui nous interresse dans ce scénario :**

```
Then the "registration" mail should be sent to "admin@example.com"
```

**Cette étape pose plusieurs problèmes :**

*   on doit d'abord être capable de récupérer l'ensemble des mails envoyés lors d'un scénario à un utilisateur
*   on doit identifier les types de mails (registration, confirmation...)

### Récupérer l'ensemble des mails

Avec SwiftMailer, il suffit de modifier la configuration (dans le fichier `config_test.yml`) pour stocker les mails sur le disque au lieu de les envoyer, en utilisant le mode spool de type file.

```
swiftmailer:
    spool:
        type: file
```

Lorsque l'on rajoute cette configuration, ou peut récupérer le dossier qui permet de stocker les messages, à partir du paramètre swiftmailer.spool.file.path depuis le container. Par défaut il s'agit du dossier `%kernel.cache_dir%/swiftmailer/spool`.

### Récupérer seulement les mails du scénario en cours

SwiftMailer va écrire un fichier par mail envoyé, pour récupérer uniquement ceux du scénario en cours, il suffit de vider le dossier qui contient ces mails avant le scénario. Pour celà, on peut créer une méthode dans le fichier `FeatureContext.php` et ajouter une annotation `@BeforeScenario` sur cette méthode afin que cette dernière soit executée avant chaque scénario.

```
<?php

use Symfony\Component\Filesystem\Filesystem;

/**
 * We need to purge the spool between each scenario
 *
 * @BeforeScenario
 */
public function purgeSpool()
{
    $spoolDir = $this->getSpoolDir();

    $filesystem = new Filesystem();

    $filesystem->remove($spoolDir);
}
```

### Identifier les type de mails

Pour identifier un mail, on ne peut pas se fier à son contenu, car celui-ci peut être internationalisé (donc traduit). J'ai choisi d'identifier les mails en ajoutant un en-tête personnalisé, car c'est une méthode très simple a appliquer avec SwiftMailer, il suffit d'une seul ligne de code. Cette méthode n'impacte en rien l'utilisateur qui recevra cet email.

```
<?php

$message->getHeaders()->addTextHeader('X-Message-ID', 'registration');
```

### Le code

Bien entendu, il faut écrire une étape personnalisée avec Behat (custom step) qui va faire tout le travail.

```
<?php

namespace Project\CoreBundle\Features\Context;

use Behat\MinkExtension\Context\MinkContext;
use Behat\Symfony2Extension\Context\KernelAwareInterface;
use Symfony\Component\HttpKernel\KernelInterface;
use Symfony\Component\Filesystem\Filesystem;
use Symfony\Component\Finder\Finder;
use Behat\Behat\Exception\BehaviorException;

class FeatureContext extends MinkContext implements KernelAwareInterface
{
    private $parameters

    private $kernel;

    public function __construct(array $parameters)
    {
        $this->parameters = $parameters;
    }

    public function setKernel(KernelInterface $kernel)
    {
        $this->kernel = $kernel;
    }

    /**
     * @Given /^(?:|the )"(?P<type>[^"]+)" mail should be sent to "(?P<email>[^"]+)"$/
     */
    public function theMailShouldBeSentTo($type, $email)
    {
        $spoolDir = $this->getSpoolDir();

        $filesystem = new Filesystem();

        if ($filesystem->exists($spoolDir)) {
            $finder = new Finder();

            // find every files inside the spool dir except hidden files
            $finder
                ->in($spoolDir)
                ->ignoreDotFiles(true)
                ->files();

            foreach ($finder as $file) {
                $message = unserialize(file_get_contents($file));

                // check the recipients
                $recipients = array_keys($message->getTo());
                if (!in_array($email, $recipients)) {
                    continue;
                }

                // check if this is the correct message type
                $headers = $message->getHeaders();
                if ($headers->has('X-Message-ID')) {
                    $messageId = $headers->get('X-Message-ID')->getValue();

                    if ($messageId == $type) {
                        return;
                    }
                }
            }
        }

        throw new BehaviorException(sprintf("The \"%s\" was not sent", $type));
    }

    /**
     * We need to purge the spool between each scenario
     *
     * @BeforeScenario
     */
    public function purgeSpool()
    {
        $spoolDir = $this->getSpoolDir();

        $filesystem = new Filesystem();

        $filesystem->remove($spoolDir);
    }

    protected function getSpoolDir()
    {
        return $this->kernel->getContainer()->getParameter('swiftmailer.spool.default.file.path');
    }
}
```

### Conclusion

Et voilà une méthode simple et propre pour vous permettre de vérifier que votre application a bien donné l'ordre d'envoyer le mail.
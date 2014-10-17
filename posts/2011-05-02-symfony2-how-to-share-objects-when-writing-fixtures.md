
This is a small post to explain to you how to simply share objects when you write your fixtures on Symfony2.

Let's assume you have 2 class,

```
    Game
```

and

```
Platform
```

linked together as a One to Many relation (one game may have One or Many Platforms).
How to tell Doctrine2 to link Platform Y to this Game X ?

```
namespace Application\SeekTeam2Bundle\DataFixtures\ORM;

use Application\SeekTeam2Bundle\Entity\Platform;
use Doctrine\Common\DataFixtures\FixtureInterface;
use Application\\SeekTeam2Bundle\Entity\Game;
use Doctrine\Common\DataFixtures\AbstractFixture; // you must add this to be able to use the setReference() /
// getReference() functions we need to share objects

// Be sure to extend AbstractFixture
class GameFixtures extends AbstractFixture
{
  public function load($em)
  {
    $this->loadPlatforms($em);
    $this->loadGames($em);
  }

  public function loadPlatforms($em)
  {

    $platform = new Platform();
    $platform->setName('PC');
    $platform->setSmallImage('pc-min.png');
    $platform->setImage('pc.png');
    $em->persist($platform);

    // by using setRerence, you'll be able to use it later when you want to associate this object to
    // another one without a single one DB query.
    $this->setReference('pc', $platform);

    $platform = new Platform();
    $platform->setName('Xbox 360');
    $platform->setSmallImage('xbox360-min.png');
    $platform->setImage('xbox360.png');
    $em->persist($platform);
    $this->setReference('xbox360', $platform);

    $platform = new Platform();
    $platform->setName('PS3');
    $platform->setSmallImage('ps3-min.png');
    $platform->setImage('ps3.png');
    $em->persist($platform);
    $this->setReference('ps3', $platform);

    $em->flush();

}

  public function loadGames($em)
  {
    $game = new Game();
    $game->setName('Counter Strike : Source');
    $game->setDescription("Counter-Strike: Source blends Counter-Strike's award...");

    // Here we can add the relation by getting the reference you previously set on the platform object
    $game->addPlatforms($this->getReference('pc'));

    $em->persist($game);
    $this->setReference('css', $game);

    $em->persist($game);

    $game = new Game();
    $game->setName('Crysis 2');
    $game->setDescription("It’s 2023, terrifying alien invaders stalk the New York City streets...");
    $game->addPlatforms($this->getReference('pc'));
    $game->addPlatforms($this->getReference('xbox360'));
    $game->addPlatforms($this->getReference('ps3'));

    $em->persist($game);
    $this->setReference('crysis2', $game);

    $em->persist($game);
    $em->flush();

  }
}
```

There is a whole chapter dedicated to Fixtures in the [cookbook][1] where advanced usage is being developped (like Ordered fixtures...). I strongly advise you to read it, especially if you are a newcomer to Symfony2.

 [1]: http://symfony.com/doc/2.0/cookbook/doctrine/doctrine_fixtures.html
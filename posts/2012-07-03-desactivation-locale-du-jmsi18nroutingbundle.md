
Après avoir configuré le [JMSI18nRoutingBundle][1] pour un projet, je me suis rendu compte d'un problème avec son API, en effet, celle-ci, normalement accessible via `/api` s'est retrouvée transformée en /en/api.

```
jms_i18n_routing:
    default_locale: en
    locales: [en, fr]
    strategy: prefix
```

Il n'y a, de plus, aucune indication dans la documentation du Bundle concernant une possible suppression du préfixe de langue, mais en regardant de plus prêt les tests on peut y trouver une option `i18n = false`, permettant de désactiver ponctuellement le comportement du bundle.

```
/**
* @Route("/api/users", name="api_users", options={"i18n" = false})
*/
public function usersAction()
```

En utilisant `app/console router:debug` on constate bien que la route n'a pas le préfixe de langue

```
en__RG__homepage ANY /en/
fr__RG__homepage ANY /fr/
api_users ANY /api/user
```

 [1]: https://github.com/schmittjoh/JMSI18nRoutingBundle "JMSI18nRoutingBundle"
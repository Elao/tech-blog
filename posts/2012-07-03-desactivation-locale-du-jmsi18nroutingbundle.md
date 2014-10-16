
Après avoir configuré le [JMSI18nRoutingBundle][1] pour un projet, je me suis rendu compte d&#8217;un problème avec son API, en effet, celle-ci, normalement accessible via /api s&#8217;est retrouvée transformée en /en/api.

<div class="codecolorer-container yaml vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="yaml codecolorer">
    <span class="co4">jms_i18n_routing</span>:<span class="co3"><br /> &nbsp; &nbsp; default_locale</span><span class="sy2">: </span>en<span class="co3"><br /> &nbsp; &nbsp; locales</span><span class="sy2">: </span><span class="br0">&#91;</span>en, fr<span class="br0">&#93;</span><span class="co3"><br /> &nbsp; &nbsp; strategy</span><span class="sy2">: </span>prefix
  </div>
</div>

Il n&#8217;y a, de plus, aucune indication dans la documentation du Bundle concernant une possible suppression du préfixe de langue, mais en regardant de plus prêt les tests on peut y trouver une option &#8220;i18n = false&#8221;, permettant de désactiver ponctuellement le comportement du bundle.

<div class="codecolorer-container php vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="php codecolorer">
    <span class="co4">/**<br /> * @Route("/api/users", name="api_users", options={"i18n" = false})<br /> */</span><br /> <span class="kw2">public</span> <span class="kw2">function</span> usersAction<span class="br0">&#40;</span><span class="br0">&#41;</span>
  </div>
</div>

En utilisant \`app/console router:debug\` on constate bien que la route n&#8217;a pas le préfixe de langue

<div class="codecolorer-container text vibrant" style="overflow:auto;white-space:nowrap;width:100%;">
  <div class="text codecolorer">
    en__RG__homepage ANY /en/<br /> fr__RG__homepage ANY /fr/<br /> api_users ANY /api/user
  </div>
</div>

 [1]: https://github.com/schmittjoh/JMSI18nRoutingBundle "JMSI18nRoutingBundle"
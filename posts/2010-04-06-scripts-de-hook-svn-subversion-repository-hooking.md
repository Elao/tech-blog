
Sommaire :

1. [Création et configuration d'un dépôt SVN](/blog/creation-et-configuration-depot-svn.html)
2. Scripts de hook SVN
3. S'authentifier et accéder à un dépôt SVN (à venir)
4. SVN over SSH (à venir)
5. Authentification via certificat (à venir)

Dans ce chapitre nous allons parler de "Hook" ou "Crochet" en français, mais il faut avouer que ça sommes moins bien

Pour faire cours un "hook" est un programme qui permet de "détourner" un traitement avant de le rendre au workflow "normal". Dans notre cas de dépôt SVN un hook va être déclenché par un évenement du référentiel (notre dépôt) comme la création d'une nouvelle révision ou la modification d'une propriété non versionnée. Un hook va être appelé avec suffisamment d'information pour déterminer de quel type est l'évenement, qu'elle est sa cible et qui la déclenché.<br /> Selon la valeur de retour de notre hook, le traitement initié sur le dépôt peut continuer, s'arrêter ou suspendre son exécution.

Comme vu dans le <a title="Création et configuration d’un dépot subversion (SVN)" href="/blog/creation-et-configuration-depot-svn.html">chapitre précédent</a> le répertoire de Hooks dispose de quelques exemples de scripts.

- post-commit.tmpl
- post-lock.tmpl
- post-revprop-change.tmpl
- post-unlock.tmpl
- pre-commit.tmpl
- pre-lock.tmpl
- pre-revprop-change.tmpl
- pre-unlock.tmpl
- start-commit.tmpl

Cette liste présente un modèle de script pour chacun des "hooks" disponible pour notre dépôt subversion. Si l'on regarde le contenu de ces scripts, et plus particulièrement celui de "start-commit.tmpl", nous pouvons voir comment les différentes informations sont récupérées:

```
REPOS="$1"
USER="$2"

# Exit on all errors.
set -e

"$REPOS"/hooks/commit-allower.pl --repository "$REPOS" --user "$USER"
"$REPOS"/hooks/special-auth-check.py --user "$USER" --auth-level 3

# All checks passed, so allow the commit.
exit 0
```

Le fonctionnement des différents script est généralement expliqué à l'intérieur des templates, si nous souhaitons, par exemple, mettre en place un traitement se déclenchant au moment ou un commit est fait, il nous suffit de créer un script appelé "start-commit". Le langage utilisé importe peu, il faut seulement que votre serveur soit capable de l'interpréter correctement, mais cela peut être un Shell Script, du C, du Python, du PHP, du Perl que sais-je, la seule contrainte à respecter étant de le nommer exactement de la même façon que le hook prévu et de bien rendre ce script exécutable.


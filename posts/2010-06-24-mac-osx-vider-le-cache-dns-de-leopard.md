Lors de la mise à jour d'un serveur de nom ou tout simplement après une modification de ses DNS, il est parfois nécessaire de "vider" le cache local de notre machine pour profiter immédiatement des dernières modifications.

**OSX dispose d'une commande permettant de le faire :**

```
sudo dscacheutil -flushcache
```

A ajouter dans votre <a title="Personnaliser son terminal" href="/blog/personnaliser-son-terminal-sous-mac-osx-configuration-avancee.html" target="_blank">.bashrc</a> car très utile !
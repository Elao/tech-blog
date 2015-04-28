## Introduction

Etant donnée que Meteor envoie tous les fichiers du projet aux clients web (hormis les dossiers spéciaux tels que `/server`, `/private`, `/public`), toute l'application est packagée et envoyée aux clients web. Cela pose plusieurs problèmes dont :
- la difficulté d'alléger la masse de code envoyée au client (templates html + helpers & libs js + styles css) ;
- la difficulté de "protéger" son application par omission de code (typiquement le code de la partie `/admin` ne devrait être envoyé qu'aux users de type admin).

Pour palier à ce problème, nous architecturons notre application en 2 sous-applications :
- le *front* pour les users standards (domaine `http://project.dev`)
- le *back* pour les administrateurs (domaine `http://admin.project.dev`)

Ces deux applications utilisent les mêmes données et doivent donc accéder à la même base de données.

## Architecture des dossiers/fichiers

```
➜  my-project  tree -a
.
├── .gitignore
├── README.md
├── back
│   ├── .meteor
│   │   ├── ...
│   ├── back.css
│   ├── back.html
│   ├── back.js
│   └── packages
│       └── collections -> ../../packages/collections
├── front
│   ├── .meteor
│   │   ├── ...
│   ├── front.css
│   ├── front.html
│   ├── front.js
│   └── packages
│       └── collections -> ../../packages/collections
└── packages
    └── collections
        ├── package.js
        ├── players.js
        └── playgrounds.js
```

Nous avons deux applications Meteor (*front* et *back*). Ces deux applications portent des liens symbolique vers des packages du dossier `/packages` afin de faire de la réutilisation de code.

## Run des applications

En local (DEV), nous prenons le parti de lancer mongo sur l'application *front* et de connecter le *back* sur le mongo du *front*.

Par défaut, Meteor lance l'**application** sur le port **3000** et ouvre une connexion **mongo** sur le port **3001**.

```
➜  front  meteor
[...]
=> App running at: http://localhost:3000/
```

Il reste à lancer l'application *back* en spécifiant l'host mongo sur lequel se connecter et le port sur lequel l'application sera accessible (3000 étant déjà occupé par le *front*).

```
➜  back  MONGO_URL=mongodb://localhost:3001/meteor meteor -p=3080
[...]
=> App running at: http://localhost:3080/
```

En local (DEV), la réactivité est assurée entre les deux applications car Meteor active l'Oplog de mongo par défaut.
Il vous faudra l'activer sur votre serveur de production pour bénéficier de l'Oplog via le paramètre **MONGO_OPLOG_URL**

## Conclusion

En conclusion, décomposer une application en plusieurs sous-applications permet, entre autres, de mieux gérer son code et d'alléger le code envoyé au client. Cela légitimise l'utilisation des packages "applicatifs" (telles que les collections, règles métiers partagées, etc.), ce qui est une bonne pratique.

Enfin, cette architecture est micro-service friendly !

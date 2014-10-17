
Cet article a pour but de vous proposer une solution qui vous permettra d'utiliser quelques propriétés de CSS3 sous IE6 , 7 et 8. Comme vous le savez, ces navigateurs ne gèrent pas les nouvelles règles introduites par CSS3.

Différentes solutions existent déjà pour permettre l'émulation de border-radius qui permet d'obtenir des coins arrondis. <a href="http://css3pie.com/" target="_blank">CSS3pie</a> permet en effet d'utiliser les propriétés

```
border-radius, linear-gradient, box-shadow, border-image
```

Très simplement , il suffit d'ajouter à votre élément HTML (qui bénéficie d'une ou de plusieurs règles cités ci-dessus) :

```
behavior: url(chemin/vers/PIE.htc);
```

Le résultat est assez bluffant, bien que moins réussi que sur les navigateurs qui gèrent ces propriétés nativement. Bien évidemment, CSS3 pie a ses avantages et ses inconvéniants.

**Les plus :**

*   Simple d'utilisation
*   Gain de temps, car on évite l'utilisation d'images
*   Pas de blocage du rendu de la page HTML contrairement à l'utilisation d'un fichier `.js`

**Les moins :**

*   Rendu moins élégant que les navigateurs gérant nativement le CSS3
*   Traitement plus lourd, à cause des tags "propriétaires" contenus dans les fichiers `.htc`

Démo (à tester sous IE) & site internet : <a href="http://css3pie.com/" target="_blank">http://css3pie.com/</a>
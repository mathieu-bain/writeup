# A star is born — Writeup

## Résumé
Writeup du challenge "World tour".

## Métadonnées
- Catégorie : forensics / audio / esoteric
- Score : 10 points

## Fichiers fournis
### Texte
La dernière composition a connu un tel succès que nous avons décidé d’organiser une tournée mondiale ! Nous avons même commencé à composer une toute nouvelle chanson spécialement pour l’occasion. Malheureusement, le disque a été corrompu, et nous n’avons pu récupérer qu’une partie de la chanson ainsi que ses paroles. Hélas, la fin manque… Peux-tu nous aider à la réparer et à découvrir les secrets qu’elle renferme ?

### Fichiers
* Un fichier chal.mp3
* Un fichier lyrics.rock

## Objectif
Récupérer le flag caché dans le challenge.

## Comment faire
### Analyse des fichiers

Lorsqu'on regarde nos 2 fichiers, ces derniers sont ouvrables mais la fin semble corrompue.

### Le fichier de paroles
Le fichier **lyrics.rock** est un fichier de code rockstar avec des caractères random à la fin.
On peut essayer de retirer ces caractères.

Seulement, si on essaie d'exécuter ce code rockstar, il ne se passe rien, donc il manque quelque chose à ajouter à la fin.

On va donc devoir analyser le code en se basant sur la doc du langage (https://codewithrockstar.com/docs/)

On remarque qu'il y a 2 fonctions qui sont définies:
* Risen
* My bus

*Note:* Les fonctions sont définies avec un nom suivi de *takes*

Ensuite, chaque ville est une variable qui contient un nombre (voir la définition de *poetic number* dans la doc).
Cette variables sont ajoutées au tableau **my tour**

#### <ins>La fonction My bus</ins>
Cette fonction semble faire appel à la fonction **Risen** à cause de la ligne suivante:
```
Let their hands be risen taking people, ice
```

Elle prend 2 entrées:
* fuel
* cocaine

Donc vraissemblablement que notre tableau **my tour** va correspondre à l'une de ces 2 variables.
La variable **fuel** doit être de type String à cause de la ligne suivante:
```
Shatter fuel into molecule
```

La fonction **Shatter** permet en réalité de séparer une string en un tableau de caractères.
Donc par déduction, **my tour** serait passé en paramètre pour **cocaine**

#### <ins>Ajout de nouvelles paroles</ins>
Aussi, la 2e ligne correspond à une variable (**my plan**) venant de l'input, donc probablement une string.
```
Listen to my plan
```

Donc on peut tenter d'ajouter un appel à la fonction **my bus** avec ces informations:
```
Shout my bus taking my plan, my tour
```

#### <ins>Test</ins>
On va essayer de voir ce que ça donne

```bash
finctf@world_tour$ rockstar ./lyrics.rock
ABCD

v
 X|Z3{
qd?x\_;qu!|
`4j3
```

On obtiens pas grand chose, mais clairement ça semble être la bonne piste. Il nous reste donc à trouver la bonne string à passer en entrée.

### Le fichier MP3
Ce coup-ci les informations ID3 ne nous donnent pas grand chose.

Par contre on peut regarder s'il n'y a pas d'informations cachées dans le fichier.

```bash
finctf@world_tour$ xxd ./chal.mp3 | tail -n 5
003bc630: 5555 5555 5555 5555 5555 5555 5555 5555  UUUUUUUUUUUUUUUU
003bc640: 5555 5555 5555 5555 5555 5555 5555 5555  UUUUUUUUUUUUUUUU
003bc650: 5555 5555 5555 5555 5555 5555 5555 5555  UUUUUUUUUUUUUUUU
003bc660: 5555 5555 5555 5555 5555 5555 4b65 793a  UUUUUUUUUUUUKey:
003bc670: 5354 3452 5230 434b 0a                   ST4RR0CK.
```

On voit _Key:ST4RR0CK_

Si on essaie avec notre fichier rockstar:
```bash
finctf@world_tour$ rockstar ./lyrics.rock
ST4RR0CK
drgn{l3ts_s1gn_4_c0ntr4ct!!}
```

## Flag
On obtient au final le flag: **drgn{l3ts_s1gn_4_c0ntr4ct!!}**
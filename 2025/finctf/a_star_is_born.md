# A star is born — Writeup

## Résumé
Writeup du challenge "A star is born".

## Métadonnées
- Catégorie : forensics / audio / esoteric
- Score : 5 points

## Fichiers fournis
### Texte
Mon ami a toujours rêvé d’être une rockstar. Malheureusement, il a toujours été un piètre musicien. Mais sais-tu dans quoi il est vraiment doué ? Travailler avec l’intelligence artificielle. Il m’a envoyé sa dernière composition et m’a dit qu’il y avait caché une petite surprise. Saura-tu découvrir ce qu’il a dissimulé ?

### Fichier
Un fichier chal.mp3

## Objectif
Récupérer le flag caché dans le challenge.

## Comment faire
### Metadonnées
On commence par écouter le fichier chal.mp3 qui n'a rien de vraiment particulier, ça semble être simplement une chanson de rock.
Cependant, si on regarde les metadonnées ça commence à devenir intéressant.

Sur Windows en allant voir dans les propriétés > détails
Sur Linux, avec un outil qui permet de récupérer les informations ID3 (comme eye-d3 par exemple)

```bash
finctf@a_star_is_born$ eyeD3 ./chal.mp3
...a_star_is_born/chal.mp3 [ 3.63 MB ]
-----------------------------------
Time: 02:41     MPEG1, Layer III        [ ~189 kb/s @ 48000 Hz - Joint stereo ]
-----------------------------------
ID3 v2.3:
title: The source code
artist: The real rockstar
album: MW10aDNwNHNzdzByZA==
track: 
-----------------------------------
```

On observe que le champ *album* semble être en base 64.
Si on le décode, on obtiens le résultat suivant:

```bash
finctf@a_star_is_born$ echo "MW10aDNwNHNzdzByZA==" | base64 -d
1mth3p4ssw0rd
```

Ce qui semble être un mot de passe.

### Fichier caché
Ce mot de passe doit donc permettre d'ouvrir quelque chose.
On pourrais utiliser un outil comme _binwalk_ pour analyser de plus près ce fichier mp3.

```bash
finctf@a_star_is_born$ binwalk chal.mp3

                              /home/finctf/a_star_is_born/chal.mp3
----------------------------------------------------------------------------------------------------------------------------------------
DECIMAL                            HEXADECIMAL                        DESCRIPTION
----------------------------------------------------------------------------------------------------------------------------------------
3809408                            0x3A2080                           ZIP archive, file count: 1, total size: 705 bytes
----------------------------------------------------------------------------------------------------------------------------------------

Analyzed 1 file for 85 file signatures (187 magic patterns) in 33.0 milliseconds
```

On a donc un fichier zip qui se cache dedans.
Une fois qu'on extrait le fichier (avec _binwalk -e_ ou avec _dd_ par exemple) on obtiens un zip qui demande un mot de passe pour être décompressé.
En utilisant le mot de passe obtenu précedemment, on arrive avec un fichier **lyric.txt**.

### Les paroles
Ce fichier **lyric.txt** semble être complètement anodin, seulement les paroles de la chanson.
Sauf que...

Il existe un langage de programmation qui se nomme _rockstar_ (https://codewithrockstar.com/).
Sur leur site on peut exécuter directement du code. Or, si on y met les paroles on obtiens un résultat !!

```bash
finctf@a_star_is_born$ rockstar ./lyric.rock | tr '\n' ' '
100 114 103 110 123 121 48 117 114 101 95 116 104 51 95 114 48 99 107 115 116 97 114 125
```

On obtient une suite de chiffres qui semblent être le code de caractères en ASCII
Avec une petite transformation awk, on arrive donc à:

```bash
finctf@a_star_is_born$ rockstar ./lyric.rock | awk '{printf("%c",$1)}'
drgn{y0ure_th3_r0ckstar}
```

## Flag
On obtient au final le flag: **drgn{y0ure_th3_r0ckstar}**
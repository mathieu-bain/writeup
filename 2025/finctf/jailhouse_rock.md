# A star is born — Writeup

## Résumé
Writeup du challenge "Jailhouse Rock".

## Métadonnées
- Catégorie : forensics / audio / esoteric
- Score : 15 points

## Fichiers fournis
### Texte
Après un nombre incalculable de tequilas, tu t’es fait arrêter et jeté en prison. Mais ne t’inquiète pas, j’ai un plan — leur sécurité est risible. Malheureusement, tu vas devoir m’aider à te faire sortir de ce pétrin en mode rock and roll.

### Fichiers
* Un fichier Dockerfile
* Un fichier chal.py

## Objectif
Se connecter sur le serveur fourni dans le provisionning et récupéré le flag qui s'y cache.

## Comment faire
### Analyse

Lorsqu'on se connecte sur le serveur, on tombe dans une sorte de prompt qui attend une entrée.
Le serveur s'attend à ce qu'on y mette un code rockstar pour nous fournir le code.

```bash
     ________
    |        |
    |  ____  |
    |  |  |  |
    |  |  |  |
    |  |__|  |
    |________|
    |   |    |
    |   |    |
    |   |    |
    |   |    |
    |___|____|
    (c) Hard Rock Penitentiary 

    
Enter your decryption function:
Finish your input with $$END$$ on a newline
___________________________________________
```

Les 2 fichiers fournis correspondent:
* à l'image Docker qui roule et sur laquelle on attérit quand on se connecte
* au script python qui est exécuté et qui attend notre code rockstar


### Le fichier Dockerfile
Le fichier Dockerfile ne nous apprend seulement que l'entrypoint est le script python et qu'il utilise la version 2.0.29 de rockstar.
Il y a bien une variable d'environnement FLAG mais celle-ci n'est pas le véritable flag.

### Le fichier python
La solution vient donc de l'analyse du fichier python.

Il y a les fonctions suivantes qui vont lire notre code rockstar et l'envoyer à l'interpreteur
1. jail
2. write_down_lyrics
3. sing
4. read_until

La 1e va mettre les contraintes suivantes dans le code:
* Pas de chiffres autorisés, on va être obligé d'utiliser des *poetic number*
* Pas de symboles spéciaux

La 3e va nous donner l'indication que notre code va être encadré par les instructions suivantes:
* au début:  
    let something be arguments at 0
  
* à la fin:  
    let liberty be decrypt taking something  
    shout liberty

Ensuite on a le bout de code d'encryption

```python
def encrypt_key(key):
    scrambled = [] 
    for i, c in enumerate(key):
        shifted_char = chr((ord(c) + (i + 1)) % 256)
        scrambled.append(shifted_char)
    scrambled_str = ''.join(scrambled)
    result = scrambled_str[::-1]
    return result
```

C'est une sorte de chiffrement de César où l'on va décaler la lettre de la clé de la valeur de sa position.
Par exemple dans ABCD, A -> B (+1), B -> D (+2), C -> F (+3) et D -> H (+4)

Enfin, le script génère aléatoirement une clé et la chiffre avec cette methode.
L'objectif est donc d'écrire un code rockstar qui va faire la procédure de déchiffrement (le script à la fin compare le résultat obtenu avec la clé générée).

### Le code Rockstar
à venir...

## Flag
On obtient au final le flag: **drgn{l3ts_s1gn_4_c0ntr4ct!!}**
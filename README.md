# bof3-translation-tool
Breath of Fire 3 Translation Tool

- [bof3-translation-tool](#bof3-translation-tool)
  - [Introduction](#introduction)
  - [Utilisation](#utilisation)
    - [Décompression des fichiers EMI](#décompression-des-fichiers-emi)
    - [Reconstruction des fichiers EMI](#reconstruction-des-fichiers-emi)
    - [Extraction de texte](#extraction-de-texte)
    - [Extraction manuelle de texte (Raw Dump)](#extraction-manuelle-de-texte-raw-dump)
    - [Traduction automatique (ML)](#traduction-automatique-ml)
    - [Réinsertion de texte](#réinsertion-de-texte)
    - [Réinsertion manuelle de texte (Raw Reinsert)](#réinsertion-manuelle-de-texte-raw-reinsert)
    - [Indexation des textes](#indexation-des-textes)
    - [Expansion du texte indexé](#expansion-du-texte-indexé)
    - [Conversion des graphismes RAW en TIM/BMP](#conversion-des-graphismes-raw-en-timbmp)
    - [Conversion des graphismes TIM/BMP en RAW](#conversion-des-graphismes-timbmp-en-raw)
    - [Séparer les graphismes RAW (split)](#séparer-les-graphismes-raw-split)
    - [Fusionner les graphismes RAW séparés (merge)](#fusionner-les-graphismes-raw-séparés-merge)
  - [Dump de tous les textes/graphismes/fichiers binaires à modifier](#dump-de-tous-les-textesgraphismesfichiers-binaires-à-modifier)
    - [Prérequis](#prérequis)
    - [Extraction](#extraction)
    - [Résultat final](#résultat-final)
  - [Réinsertion de tous les textes/graphismes/fichiers binaires modifiés](#réinsertion-de-tous-les-textesgraphismesfichiers-binaires-modifiés)
    - [Prérequis](#prérequis-1)
    - [Réinsertion](#réinsertion)
    - [Résultat final](#résultat-final-1)
  - [Ce qui manque](#ce-qui-manque)

## Introduction
Cet outil a été développé pour décompresser et reconstruire les fichiers au format **EMI** de Breath of Fire 3 PSX/PSP. 

Outre la gestion des **EMI**, il prend également en charge l'extraction et la réinsertion des textes de jeu dans les fichiers **EMI** et permet l'indexation des textes dupliqués dans divers fichiers dans un seul fichier et, à l'inverse, leur expansion dans plusieurs fichiers.

Il intègre également la gestion des graphismes **RAW** dans **TIM**/**BMP** et le réarrangement des tuiles à l'intérieur de ceux-ci (**RAW** ne sont pas "triés").

## Utilisation
L'utilisation de l'outil est très simple :

```
python bof3tool.py -h
```

Par exemple, il montrera ce qu'il peut faire ;

```
usage: bof3tool.py [-h] [-v] {unpack,pack,dump,rawdump,translate,reinsert,rawreinsert,index,expand,raw2tim,tim2raw,raw2bmp,bmp2raw,split,merge} ...

Breath of Fire III Tool (PSX/PSP)

positional arguments:
  {unpack,pack,dump,rawdump,translate,reinsert,rawreinsert,index,expand,raw2tim,tim2raw,raw2bmp,bmp2raw}
                        Description
    unpack              unpack EMI files into bin files
    pack                pack bin files into EMI file
    dump                dump text from bin file
    rawdump             raw dump bytes from file
    translate           translate a JSON file using Amazon Translate (ML)
    reinsert            reinsert text into bin file
    rawreinsert         raw reinsert bytes into file
    index               index all texts into single file
    expand              expand an indexed file into multiple files
    raw2tim             convert graphic RAW to TIM (PSX)
    tim2raw             convert TIM (PSX) to graphic RAW
    raw2bmp             convert graphic RAW to BMP
    bmp2raw             convert BMP to graphic RAW
    split               split raw image
    merge               merge splitted raw image

optional arguments:
  -h, --help            show this help message and exit
  -v, --version         show program's version number and exit
```

Les commandes disponibles sont les suivantes :
* **unpack**: extrait le contenu des fichiers **EMI**.
* **pack**: reconstruit le fichier **EMI** précédemment extrait
* **dump**: extrait le texte d'un fichier **bin** dans un fichier texte **JSON**.
* **rawdump**: extrait des séquences d'octets de façon répétée d'un fichier sous forme de **JSON**.
* **translate**: traduit un fichier texte **JSON** en utilisant **Amazon Translate (ML)**.
* **reinsert**: convertit un fichier texte **JSON** au format **bin**.
* **rawreinsert**: insère des séquences d'octets de façon répétée dans un fichier à partir d'un **JSON**.
* **index**: indexe plusieurs fichiers texte dans un seul fichier (utile pour les textes répétitifs)
* **expand**: développe un fichier indexé dans les fichiers textes originaux (textes répétés)
* **raw2tim**: convertit un fichier graphique **RAW** au format **TIM** en réarrangeant les tuiles
* **tim2raw**: convertit un fichier au format **TIM** en graphismes **RAW** en réarrangeant les tuiles
* **raw2bmp**: convertit un fichier graphique **RAW** au format **BMP** en réarrangeant les tuiles
* **bmp2raw**: convertit un fichier au format **BMP** en graphismes **RAW** en réarrangeant les tuiles
* **split**: divise un fichier graphique **RAW** en plusieurs fichiers
* **merge**: fusionne plusieurs fichiers graphismes **RAW** en un seul fichier

### Décompression des fichiers EMI
Pour extraire un ou plusieurs fichiers **EMI**, il suffit de donner un :
```
python bof3tool.py unpack -i BIN/WORLD00/*.EMI -o unpacked/WORLD00
```

Ce faisant, nous dirons qu'il faut décompresser tous les fichiers **EMI** contenus dans le dossier `BIN/WORLD00` à l'intérieur du dossier `unpacked/WORLD00`.

A l'intérieur, nous trouverons une structure composée de :
* un fichier **JSON** contenant les informations du fichier original
* un dossier contenant les fichiers extraits de l'**EMI**.

Si nous ajoutons à la commande ci-dessus les paramètres `--dump-text`, `--dump-graphic` et `--extra-table 9A=à 9B=ò...` nous aurons également :
* tout dump du texte dumpé au format **JSON** (il peut ne pas s'agir de texte de jeu mais de matériel de débogage)
* les graphismes exportés au format **BMP** conformément à la carte des fichiers connus contenant des graphismes à traduire (uniquement pour le débogage, palette prédéfinie)
* tous les caractères supplémentaires (par exemple, les accents) seront traités dans le vidage du texte

Exemple d'extraction d'un seul fichier :
```
python bof3tool.py unpack -i AREA033.EMI -o unpacked --dump-text --dump-graphic
```

Résultat:
```
--- Breath of Fire III Tool (PSX/PSP) ---

Unpacking AREA033.EMI into unpacked/AREA033.json and data blocks(14)...
--- Breath of Fire III Tool (PSX/PSP) ---

Unpacking AREA033.EMI into unpacked/AREA033.json and data blocks(14)...
unpacked/AREA033/AREA033.1.bin created (Sound)
unpacked/AREA033/AREA033.2.bin created 
unpacked/AREA033/AREA033.3.bin created 
unpacked/AREA033/AREA033.4.bin created 
unpacked/AREA033/AREA033.5.bin created 
unpacked/AREA033/AREA033.6.bin created (RAW Graphic)
Coverting RAW AREA033.6.bin in BMP unpacked/AREA033/AREA033.6.bin.8b.64w.64x32.1024r.bmp using 8bpp, final width size 1024 using tile of 64x32...
Done
unpacked/AREA033/AREA033.7.bin created (CLUTs)
unpacked/AREA033/AREA033.8.bin created (RAW Graphic)
Coverting RAW AREA033.8.bin in BMP unpacked/AREA033/AREA033.8.bin.8b.64w.64x32.1024r.bmp using 8bpp, final width size 1024 using tile of 64x32...
Done
unpacked/AREA033/AREA033.9.bin created (CLUTs)
unpacked/AREA033/AREA033.10.bin created 
unpacked/AREA033/AREA033.11.bin created 
unpacked/AREA033/AREA033.12.bin created (Text blocks)
Dumping 256 strings from block0 of unpacked/AREA033/AREA033.12.bin into unpacked/AREA033/AREA033.12.bin.json...
Text dumped
unpacked/AREA033/AREA033.13.bin created 
unpacked/AREA033/AREA033.14.bin created 
EMI AREA033.EMI unpacked into 14 files
```

### Reconstruction des fichiers EMI
Une fois que les fichiers **EMI** ont été extraits dans leurs **bin** respectifs, un ou plusieurs fichiers **EMI** peuvent être reconstruits à l'aide de la commande suivante :

```
python bof3tool.py pack -i unpacked/AREA000.json -o output
```

Le résultat sera le suivant :
```
--- Breath of Fire III Tool (PSX/PSP) ---

Packing unpacked/AREA000.json into output/AREA000.EMI...
output/AREA000.EMI created.
```

> **ATTENTION** : il peut arriver que, pendant la traduction des textes, le nouveau fichier à insérer soit plus volumineux que l'original.
>
> Dans ce cas, l'outil appliquera la logique suivante :
> - si le fichier est légèrement plus grand et peut tenir dans la zone de remplissage originale du bloc (les blocs font 2048 octets), il sera réinséré sans problème puisqu'il consomme la zone de remplissage déjà présente
> - si le fichier dépasse la taille originale et consomme tout le padding disponible, le fichier dans l'**EMI** sera étendu automatiquement tant qu'il est plus petit que la **limite maximale de 0x5800 (22528) octets**.
> - si le fichier **dépasse** la **limite de 0x5800 (22528)** octets, le reconditionnement entrera en **erreur**.

### Extraction de texte
Pour extraire le texte du jeu au format **pointers+text**, nous pouvons utiliser la commande `dump` sur le fichier **bin** contenant le texte :

```
python bof3tool.py dump -i unpacked/AREA000/AREA000.12.bin -o AREA000.12.bin.json --extra-table 9A=à 9B=ò...
```

Le résultat sera le suivant :
```
--- Breath of Fire III Tool (PSX/PSP) ---

Dumping 256 strings from block0 of unpacked/AREA000/AREA00.12.bin into unpacked/AREA000/AREA000.12.bin.json...
Text dumped.
```

Le **fichier JSON** créé contiendra le texte encodé en **UTF-8** et toutes les commandes (pauses, positions, noms etc.) telles que :

```json
{
  "block0": [
      "<POS 81>   Spring in McNeil<TIME 20><END>",
      "\"Ah...spring...<END>",
      "\"It looks like it'll<NL>be a good crop this<NL>year...<CLEAR>That means we can<NL>take it easy this<NL>year!<END>",
      "\"Not only do we have<NL>to worry about bad<NL>crops...<CLEAR>In the country, we've<NL>got taxes to<NL>worry about too...<CLEAR>I wish I could move<NL>to a real city...<NL>like Wyndia<END>",
      "\"I guess y'all aren't<NL>as bad as I thought...<CLEAR>Good job!<END>",
      "\"What're you doing,<NL>this time of night?<CLEAR>Sleep during the day<NL>an' stay up all night?<NL>S'weird, if ya ask me<END>",
      ...
  ]
}
```

De plus, si le paramètre optionnel `--extra-table` a été ajouté, tous les caractères supplémentaires tels que les accents dans l'exemple précédent seront également vidés.

### Extraction manuelle de texte (Raw Dump)
De nombreux binaires du jeu contiennent du texte, toutefois il ne s'agit pas du schéma habituel pointeur+texte, mais de morceaux d'exécutables PSX/PSP qui doivent être édités manuellement. Dans ces fichiers, on peut voir des motifs répétés à des distances fixes. La fonction `rawdump` est utilisée pour gérer ces situations.

Un exemple de fichier "brut" contenant du texte est **GAME.1.bin** (ci-dessous, le fichier extrait de la version **PSX NTSC/USA**) :

![GAME.1.bin Example](./img/GAME.1.bin.png)

Comme on peut le voir, le bloc de texte ne se trouve pas au début du fichier mais commence à un **décalage** plus loin (**0x33164**) et commence au mot ***Nothing*** et est toujours incorporé dans des **quantités** de 12 octets, **répétées** sur plusieurs lignes. Pour passer d'un objet à l'autre, il faut **décaler** de 6 octets supplémentaires pour atteindre l'objet suivant.

Si nous voulions extraire ce bloc de texte, nous pourrions utiliser la commande suivante, par exemple :

```
python bof3tool.py rawdump -i GAME.1.bin --offset 0x33164 --quantity 12 --skip 6 --repeat 92 --trim
```

Le résultat sera le suivant :
```
--- Breath of Fire III Tool (PSX/PSP) ---

Raw dumped 12 byte of raw text from GAME.1.bin into GAME.1.bin.json 92 times
```

En faisant cela, nous obtiendrons un **fichier JSON GAME.1.bin.json** contenant les 92 objets extraits du fichier original sous cette forme :

```json
{
    "file": "GAME.1.bin",
    "raw_dumps": [
        {
            "data": {
                "offset": 209252,
                "quantity": 12,
                "skip": 6,
                "repeat": 92
            },
            "dump": [
                "Nothing",
                "Green Apple",
                "Bread",
                "Healing Herb",
                "Vitamin",
                "MultiVitamin",
                "Vitamins",
                ...
            ]
        }
    ]
}
```

> **Attention** : il y a un `--trim` au bas de la commande, il est utilisé pour supprimer les **octets 0x00** excédentaires lorsque 12 caractères sont atteints.
>
> Veuillez noter que **vous ne pouvez pas dépasser la limite physique de 12 octets** pendant la traduction (pour cet exemple).

**Attention** : là encore, il est possible d'utiliser le paramètre `--extra-table` pour ajouter des caractères supplémentaires à l'extraction.

Le fichier **JSON** généré contiendra toutes les informations nécessaires pour réinsérer le texte dans ce fichier spécifique. Nous pouvons l'imaginer comme une sorte de ***patch*** à appliquer au fichier original.

Nous pouvons éventuellement ajouter d'autres vidages bruts au même **JSON** en effectuant de nouvelles extractions :

```
python bof3tool.py rawdump -i GAME.1.bin --offset 0x338DC --quantity 12 --skip 12 --repeat 83 --trim
```

Résultat:
```
--- Breath of Fire III Tool (PSX/PSP) ---

File GAME.1.bin.json with 1 raw dumps already exists, appending new raw dump...
Raw dumped 12 byte of raw text from GAME.1.bin into GAME.1.bin.json 83 times
```

Enfin, on peut souhaiter extraire des octets sans les décoder (c'est-à-dire obtenir **<HEX xx>**) : dans ce cas, on peut utiliser le paramètre `--raw`, par exemple :
```
python bof3tool.py rawdump -i GAME.1.bin --offset 0x20000 --quantity 5 --raw
```

Résultat:
```
--- Breath of Fire III Tool (PSX/PSP) ---

Raw dumped 5 byte of raw data from GAME.1.bin into GAME.1.bin.json 1 times
```

Ce qui correspond à un **JSON** avec :
```json
{
    "file": "GAME.1.bin",
    "raw_dumps": [
        {
            "data": {
                "offset": 131072,
                "quantity": 5,
                "skip": 0,
                "repeat": 1
            },
            "dump": [
                "<HEX 58><HEX 62><HEX 42><HEX 94><HEX 00>"
            ]
        }
    ]
}
```

### Traduction automatique (ML)
En utilisant la fonction `translate`, il est possible d'automatiser la traduction d'un texte en exploitant une intelligence artificielle.

**ATTENTION** : la traduction automatique n'est utile que pour obtenir un "premier jet" des textes traduits et/ou pour faire une comparaison avec ce qui serait traduit automatiquement à l'aide de l'apprentissage automatique au début de l'année 2023.
>
> La traduction proposée contient plusieurs erreurs telles que
> - traduction décontextualisée et/ou incorrecte
> - caractères transformés/espacés (par exemple, doubles guillemets)
> des espaces blancs ajoutés là où ils ne sont pas nécessaires (et donc de l'espace perdu)
> des codes de contrôle décalés, etc.

Son utilisation est très simple :
```
python bof3tool.py translate -h
```

```
usage: bof3tool.py translate [-h] -i INPUT [-o OUTPUT] [--source-language SOURCE_LANG] --target-language TARGET_LANG [--verbose]

optional arguments:
  -h, --help            show this help message and exit
  -i INPUT, --input INPUT
                        input .JSON file
  -o OUTPUT, --output OUTPUT
                        output .JSON file
  --source-language SOURCE_LANG
                        source language code (default en)
  --target-language TARGET_LANG
                        target language code (fr, de, it...)
  --verbose             show verbose logs
```

Par exemple, pour traduire tous les textes indexés dans un seul fichier :
```
python bof3tool.py translate -i strings_en.json -o strings_it.json --target-language it
```

Après quelques minutes (en fonction du nombre de lignes de texte que vous souhaitez traduire), vous obtiendrez :
```
--- Breath of Fire III Tool (PSX/PSP) ---

Translating 6366 strings of blocks from 'en' to 'it' using Amazon Translate (ML)...
File strings_en.json translated into strings_it.json from 'en' to 'it' using Amazon Translate (ML).
6365 strings translated for a total of 608684 characters.
```

Pour rappel, la traduction proposée **doit être vérifiée phrase par phrase et corrigée**.

Dans le dépôt, dans le dossier ***autotranslate*** vous pouvez trouver les textes originaux de la version PSX du jeu dans le fichier `strings_fr.json` et la **traduction italienne à réviser** proposée par Amazon Translate dans le fichier `strings_fr.json`.

### Réinsertion de texte
De même, il est possible de reconstruire un fichier texte **bin** à partir d'un **JSON** en utilisant :

```
python bof3tool.py reinsert -i AREA000.12.bin.json -o AREA000.12.bin --extra-table 9A=à 9B=ò...
```

Le résultat sera le suivant :
```
--- Breath of Fire III Tool (PSX/PSP) ---

Reinserting 256 strings from block0 of AREA000.12.bin.json into AREA000.12.bin...
Text reinserted.
```

Encore une fois, la présence du paramètre optionnel `--extra-table` permet à tous les caractères UTF8 dans le dump d'être traités avec leurs valeurs d'octets correspondantes (par exemple, la lettre **à** sera insérée en tant qu'octet **0x9A** et ainsi de suite).

Le nouveau fichier **bin** obtenu peut être réinséré dans le fichier **EMI**.

### Réinsertion manuelle de texte (Raw Reinsert)
Si nous avons extrait du texte en utilisant le mode **Raw Dump** et que nous souhaitons le réinsérer dans le fichier source original, nous pouvons utiliser la fonctionnalité `rawreinsert`.

Son fonctionnement nécessite le fichier **JSON** contenant les modifications à appliquer et éventuellement le **fichier** original (s'il est différent de celui spécifié dans le **JSON**).

Pour réinsérer, nous pouvons utiliser la commande `rawreinsert` :

```
python bof3tool.py rawreinsert -i GAME.1.bin.json
```

> **ATTENTION** : on peut ajouter le paramètre `-b filename` ou `--bin filename` pour indiquer le fichier à modifier.

Le résultat sera le suivant :
```
--- Breath of Fire III Tool (PSX/PSP) ---

File GAME.1.bin.json contains 2 raw dumps, reinserting...
Raw reinserted 12 byte of new encoded text from GAME.1.bin.json into GAME.1.bin 92 times (1 of 2 raw dump)
Raw reinserted 12 byte of new encoded text from GAME.1.bin.json into GAME.1.bin 83 times (2 of 2 raw dump)
Raw reinserted all raw dumps
```

**ATTENTION** : encore une fois, il est possible d'utiliser le paramètre `-extra-table` pour ajouter des caractères supplémentaires dans l'entrée.

### Indexation des textes
De nombreux fichiers texte de Breath of Fire III contiennent du texte répété car il s'agit simplement des mêmes scènes avec de légères variations (temps/personnage).

Afin d'éviter de retraduire les mêmes phrases à plusieurs reprises, il est possible de les indexer dans un grand fichier qui contiendra en fait le texte de l'ensemble du jeu ainsi qu'un fichier "pointeurs" pour une expansion future.

Une fois que vous avez regroupé les **JSON** de tous les textes dans un dossier, il suffit d'exécuter la commande suivante pour indexer tous les fichiers :
```
python bof3tool.py index -i texts/*.json --output-strings strings_en.json --output-pointers pointers_en.json
```

Le résultat sera, par exemple, le suivant
```
--- Breath of Fire III Tool (PSX/PSP) ---

Indexing 169 JSON files into strings_en.json/pointers_en.json...
Indexed 6365 strings (4475 repeated strings) for block0.
```

### Expansion du texte indexé
À partir d'un fichier texte indexé et de ses "pointeurs", il est possible d'obtenir les fichiers **JSON** originaux qui peuvent être retransformés en **bin** pour être réinsérés dans l'**EMI**.

Pour effectuer l'expansion, nous pouvons utiliser la commande suivante :
```
python bof3tool.py expand --input-strings strings_en.json --input-pointers pointers_en.json -o expanded
```

La commande reconstruira les fichiers **JSON** originaux dans le dossier `expanded` :

```
--- Breath of Fire III Tool (PSX/PSP) ---

Expanding 169 files...
File AREA000.12.bin.json with 256 strings recreated.
File AREA001.12.bin.json with 256 strings recreated.
File AREA002.8.bin.json with 256 strings recreated.
...
File AREA198.12.bin.json with 256 strings recreated.
File AREA199.12.bin.json with 256 strings recreated.
Expanded 169 files using 10840 indexed strings.
```

### Conversion des graphismes RAW en TIM/BMP
En utilisant les fonctions `raw2tim` et `raw2bmp`, il est possible de convertir des graphismes **RAW** en **TIM**/**BMP** et de réorganiser les tuiles en interne.

Pour expliquer son fonctionnement et montrer son utilisation, nous prendrons comme exemple le fichier `ETC/FIRST.EMI` de la version PSX.
Nous commençons par extraire son contenu :

```
python bof3tool.py unpack -i FIRST.EMI   
```

Nous obtiendrons :
```
--- Breath of Fire III Tool (PSX/PSP) ---

Unpacking FIRST.EMI into FIRST.json and data blocks(14)...
FIRST/FIRST.1.bin created (Sound)
FIRST/FIRST.2.bin created 
FIRST/FIRST.3.bin created 
FIRST/FIRST.4.bin created (RAW Graphic)
FIRST/FIRST.5.bin created (RAW Graphic)
FIRST/FIRST.6.bin created (RAW Graphic)
FIRST/FIRST.7.bin created (RAW Graphic)
FIRST/FIRST.8.bin created (RAW Graphic)
FIRST/FIRST.9.bin created (CLUTs)
FIRST/FIRST.10.bin created (CLUTs)
FIRST/FIRST.11.bin created (CLUTs)
FIRST/FIRST.12.bin created (Text blocks)
FIRST/FIRST.13.bin created (RAW Graphic)
FIRST/FIRST.14.bin created (CLUTs)
EMI FIRST.EMI unpacked into 14 files
```
Certains de ces fichiers contiennent des **graphismes RAW** tandis que d'autres contiennent le **CLUT** (les détails sont également présents dans le **JSON** `FIRST.json`), comme le fichier `FIRST.4.bin`.

Voyons donc ce qu'il est possible de faire avec la fonction `raw2tim` :

```
python bof3tool.py raw2tim -h
```

```
usage: bof3tool.py raw2tim [-h] -i [INPUT ...] [-o OUTPUT] --bpp {4,8} --width {64,128,256,512} [--tile-width TILE_W] [--tile-height TILE_H] [--resize-width RESIZE_WIDTH] [--clut CLUT]

optional arguments:
  -h, --help            show this help message and exit
  -i [INPUT ...], --input [INPUT ...]
                        input .bin (RAW) files
  -o OUTPUT, --output OUTPUT
                        output .TIM (PSX) file
  --bpp {4,8}           bits per pixel
  --width {64,128,256,512}
                        image width
  --tile-width TILE_W   tile width
  --tile-height TILE_H  tile height
  --resize-width RESIZE_WIDTH
                        resize width
  --clut CLUT           import CLUTs file
```

Nous pouvons ensuite essayer de convertir le fichier en **TIM** (ou **BMP**) en utilisant :
```
python bof3tool.py raw2tim -i FIRST/FIRST.4.bin --bpp 4 --width 128 --clut FIRST/FIRST.9.bin
````

> Pour obtenir un **BMP**, il suffit d'utiliser `raw2bmp` avec les mêmes paramètres. Si vous utilisez un fichier **CLUT** et que vous le convertissez en **BMP**, vous devez ajouter le paramètre `--palette n` spécifiant quelle **palette** (où **n** est le numéro de la **palette**, par exemple 3) utiliser car le **BMP** ne supporte qu'une **palette à la fois**.

Nous obtiendrons un **TIM** :
```
--- Breath of Fire III Tool (PSX/PSP) ---

Coverting RAW FIRST.4.bin in TIM FIRST/FIRST.4.bin.4b.128w.tim using 4bpp, size 128x512...
Done
```

L'image que nous obtiendrons sera la suivante :

![Font not arranged](./img/FIRST.4.bin.4b.128w.bmp)

Comme on peut le voir, l'image est correcte en termes de paramètres, mais il est évident qu'elle est divisée en interne en carreaux de 128x32 (le deuxième bloc verticalement devrait en fait être déplacé à droite du premier bloc) selon ce critère :
- Tile 1
- Tile 2
- Tile 3
- Tile 4
- Tile 5
- Tile 6
- Tile 7
- Tile 8
- ...

Il devrait devenir :
- Tile 1 - Tile 2
- Tile 3 - Tile 4
- Tile 5 - Tile 6
- Tile 7 - Tile 8
- ...

Il est donc possible de réorganiser les tuiles en interne en spécifiant la taille des tuiles (128x32 dans ce cas) et la largeur finale de l'image (256) :

```
python bof3tool.py raw2tim -i FIRST/FIRST.4.bin --bpp 4 --width 128 --tile-width 128 --tile-height 32 --resize-width 256 --clut FIRST/FIRST.9.bin
```

Nous obtiendrons:
```
--- Breath of Fire III Tool (PSX/PSP) ---

Coverting RAW FIRST.4.bin in TIM FIRST/FIRST.4.bin.4b.128w.128x32.256r.tim using 4bpp, final width size 256 using tile of 128x32...
Done
```

La nouvelle image réarrangée sera :

![Font rearranged](./img/FIRST.4.bin.4b.128w.128x32.256r.bmp)

C'est nettement mieux, non ?

> Lorsque l'on travaille avec **BMP**, il est possible de passer le paramètre `--negative` pour utiliser une palette de couleurs négatives, par exemple :
> 
> ![Font rearranged](./img/FIRST.4.bin.4b.128w.128x32.256r_negative.bmp)
> 
> Avec **TIM**, ce n'est pas nécessaire car **TIM** généré sans indiquer la palette possède déjà les deux palettes prédéfinies ou le **CLUT** importé en interne.

### Conversion des graphismes TIM/BMP en RAW
En utilisant les fonctions `tim2raw` et `bmp2raw`, nous pouvons effectuer le processus inverse du précédent.

Les paramètres sont pratiquement les mêmes :

```
python bof3tool.py tim2raw -h
```

```
usage: bof3tool.py tim2raw [-h] -i [INPUT ...] [-o OUTPUT] --bpp {4,8} [--tile-width TILE_W] [--tile-height TILE_H] [--resize-width RESIZE_WIDTH]

optional arguments:
  -h, --help            show this help message and exit
  -i [INPUT ...], --input [INPUT ...]
                        input .TIM (PSX) files
  -o OUTPUT, --output OUTPUT
                        output .bin (RAW)
  --tile-width TILE_W   tile width
  --tile-height TILE_H  tile height
  --resize-width RESIZE_WIDTH
                        resize width
```

En suivant l'exemple précédent, nous pouvons récupérer le fichier RAW (**bin**) en lançant un :

```
python bof3tool.py tim2raw -i FIRST.4.bin.4b.128w.128x32.256r.tim -o FIRST.4.bin --tile-width 128 --tile-height 32 --resize-width 128
```

**WARNING** : attention à `--resize-width` qui revient à **128** comme `--width` original (quand le raw2tim a été exécuté) car nous devons réarranger les tuiles comme dans le fichier original.

Nous obtiendrons:
```
--- Breath of Fire III Tool (PSX/PSP) ---

Extracting TIM info from FIRST.4.bin.4b.128w.128x32.256r.tim...
Coverting TIM FIRST.4.bin.4b.128w.128x32.256r.tim in RAW graphic FIRST.4.bin using 8bpp, final width size 128 using tile of 128x32...
Done
```

Le fichier `FIRST.4.bin` est maintenant prêt à être réinséré dans le **EMI** d'origine.

> Encore une fois, le processus avec les BMPs est le même en utilisant `raw2bmp`. La seule mise en garde est de spécifier également les plans de bits utilisés via `--bpp`.
> 
**Attention** : si vous convertissez un BMP "négatif", il n'est pas nécessaire de spécifier d'autres paramètres car la palette de couleurs n'est pas présente dans le RAW.

### Séparer les graphismes RAW (split)
On peut parfois rencontrer des graphismes **RAW** un peu particuliers, tels que `AREA030.14.bin`.

Nous convertissons de manière classique :
```
python bof3tool.py raw2tim -i AREA030.14.bin --bpp 8 --width 64 --tile-width 64 --tile-height 32 --resize-width 1024 --clut AREA030.15.bin
```

Résultat:
```
--- Breath of Fire III Tool (PSX/PSP) ---

Coverting RAW AREA030.14.bin in TIM AREA030.14.bin.8b.64w.64x32.1024r.tim using 8bpp, final width size 1024 using tile of 64x32...
Done
```

L'image obtenue est la suivante :

![AREA030.14 Full](./img/AREA030.14_full.bmp)

Comme vous pouvez le constater, il s'agit d'une image **composée de quatre images distinctes**.

Attention cependant à un détail, la **quatrième image** n'est pas destinée à être rendue à **8BPP** mais plutôt à **4BPP** car la texture entière que nous manipulons sera divisée en **pages** dans la **VRAM**.

C'est là que la fonctionnalité `split` des images **RAW** (qui, si je me souviens bien, sont organisées d'une manière particulière à l'intérieur des fichiers) nous vient en aide :

```
python bof3tool.py split -i AREA030.14.bin -o . --bpp 8 --tile-width 64 --tile-height 32 --resize-width 1024 --quantity 4
```

Résultat:

```
--- Breath of Fire III Tool (PSX/PSP) ---

Splitting RAW AREA030.14.bin into 4 parts using 64x32 tile from original width of 1024...
Done
```

De cette façon, nous demandons de diviser le **RAW** réarrangé en 4 images (qui auront 256 pixels de large) : `AREA030.14.bin.1`, `AREA030.14.bin.2`, `AREA030.14.bin.3` et `AREA030.14.bin.4`.

> **Attention** : nous pouvons spécifier un dossier de destination différent avec le paramètre `--output`, sinon le nom du fichier d'entrée sans extension sera utilisé.
> 
> Dans l'exemple que nous venons de voir, nous avons utilisé le **. (point)** pour indiquer le **dossier courant**.

À ce stade, nous pouvons essayer de convertir la **RAW** résultante comme d'habitude, en tenant compte de la nouvelle largeur :

```
python bof3tool.py raw2tim -i AREA030.14.bin.1 --bpp 8 --width 64 --tile-width 64 --tile-height 32 --resize-width 256 --clut AREA030.15.bin
```

Résultat:

```
--- Breath of Fire III Tool (PSX/PSP) ---

Coverting RAW AREA030.14.bin.1 in TIM AREA030.14.bin.1.8b.64w.64x32.256r.tim using 8bpp, final width size 256 using tile of 64x32...
Done
```

Ce qui correspond à l'image suivante :

![AREA030.14.1](./img/AREA030.14.bin.1_8bpp.bmp)

Il est évident que si nous répétons la même commande pour le **quatrième**, nous aurons le même problème qu'au début :
```
python bof3tool.py raw2tim -i AREA030.14.bin.4 --bpp 8 --width 64 --tile-width 64 --tile-height 32 --resize-width 256 --clut AREA030.15.bin
```

Résultat:
```
--- Breath of Fire III Tool (PSX/PSP) ---

Coverting RAW AREA030.14.bin.4 in TIM AREA030.14.bin.4.8b.64w.64x32.256r.tim using 8bpp, final width size 256 using tile of 64x32...
Done
```

Ce qui correspond à l'image suivante :

![AREA030.14.4 8BPP](./img/AREA030.14.bin.4_8bpp.bmp)

Cependant, il suffira de le convertir avec les bons paramètres :
```
python bof3tool.py raw2tim -i AREA030.14.bin.4 --bpp 4 --width 128 --tile-width 128 --tile-height 32 --resize-width 512 --clut AREA030.15.bin
```

Résultat:
```
--- Breath of Fire III Tool (PSX/PSP) ---

Coverting RAW AREA030.14.bin.4 in TIM AREA030.14.bin.4.4b.128w.128x32.512r.tim using 4bpp, final width size 512 using tile of 128x32...
Done
```

Vous obtiendrez ainsi une image correcte (n'oubliez pas qu'il s'agit de palettes multiples, les couleurs peuvent différer dans le **BMP** présenté ici) :

![AREA030.14.4 4BPP](./img/AREA030.14.bin.4_4bpp.bmp)

### Fusionner les graphismes RAW séparés (merge)
Si nous avons utilisé la fonctionnalité précédente pour diviser un **RAW** en plusieurs images afin de les éditer, et que nous voulons récupérer le fichier initial, nous pouvons utiliser la fonctionnalité `merge` pour reconstruire le fichier :

```
python bof3tool.py merge -i AREA030.14.bin.1 AREA030.14.bin.2 AREA030.14.bin.3 AREA030.14.bin.4 --bpp 8 --tile-width 64 --tile-height 32 --resize-width 1024
```

Résultat:

```
--- Breath of Fire III Tool (PSX/PSP) ---

Merging 4 files into AREA030.14.bin using 64x32 tile from original width of 1024...
Done
```

Le fichier original `AREA030.14.bin` sera reconstruit à partir des images séparées.

> **Attention** : n'oubliez pas d'utiliser le bon `--resize-width` qui sera la somme de toutes les largeurs de toutes les images.
>
> De plus, en utilisant le paramètre `--output` nous pouvons spécifier un nom pour le fichier de sortie, alternativement le nom du premier fichier à être fusionné sans l'extension sera utilisé.

## Dump de tous les textes/graphismes/fichiers binaires à modifier
Dans le référentiel, un script bash `dump.sh` est fourni pour automatiser l'exportation de tout le contenu à traduire/éditer.

Le script est destiné à être exécuté via un terminal **bash** et exploite divers utilitaires UNIX (par exemple find et rsync) mais devrait être compatible avec **Windows**, **Linux** et **MacOS** (à condition que vous disposiez du terminal).

### Prérequis
Le script `dump.sh` est destiné à être exécuté dans un dossier contenant la structure du fichier **Breath of Fire 3** **PSX** ou **PSP**.

En supposant que vous disposiez de l'**ISO** du jeu (**PSX PAL**, **PSX USA** ou **PSP**), vous devrez exporter le dossier `BIN` (pour les versions **PSX**) ou le dossier `USA` (pour la version **PSP**) et les renommer respectivement **PSX_PAL**, **PSX_USA** ou **PSP**.

Enfin, pour la version **PSP** uniquement, le fichier **BOOT.BIN** (situé dans le **ISO** du jeu au chemin `PSP_GAME/SYSDIR/BOOT.BIN`) doit également être ajouté au dossier **PSP**.

> Vous pouvez copier le dossier **PSX_PAL**/**PSX_USA**/**PSP** directement dans ce dépôt.

Par exemple, nous obtiendrons un dossier avec la structure suivante :

- PSX_PAL
  - BATTLE
  - BENEMY
  - BGM
  - BMAG_XA
  - BMAGIC
  - BOSS
  - BPLCHAR
  - ETC
  - PLCHAR
  - SCE_XA
  - SCENARIO
  - WORLD00
  - WORLD01
  - WORLD02
  - WORLD03
  - WORLD04

La version **PSP** sera légèrement différente :

- PSP
  - **BOOT.BIN** (en plus des fichiers)
  - BATTLE
  - BENEMY
  - BGM
  - BMAG_XA
  - BMAGIC
  - ...

À ce stade, nous sommes prêts pour l'extraction.

### Extraction
Pour exporter le contenu, il suffit de lancer l'outil sur le dossier d'origine :

```./dump.sh PSX_PAL```

Le script commencera à travailler sur le contenu du dossier du jeu en effectuant les étapes suivantes :

1. Décompressez tous les fichiers **.EMI** et extrayez le texte.
2. Supprimer tous les dumps inutiles (scènes de débogage)
3. Déplacer les dumps du jeu et du menu dans les dossiers finaux.
4. Extraire les noms de tous les ennemis
5. Extraire manuellement certains éléments de menu à partir de fichiers binaires
6. Copier et diviser tous les graphismes **RAW**.
7. Convertir les graphismes **RAW** en **TIM**.
8. Déplacer les graphismes **TIM** vers les dossiers finaux
9. Copier les fichiers binaires à éditer manuellement
10. Créer des dossiers pour l'injection de fichiers (avant et/ou après la réinsertion)
11. Indiquer les dumps de jeu, les menus et les ennemis

### Résultat final
À la fin de la procédure, nous obtiendrons les dossiers/fichiers suivants :

- BINARY
  - PSX_PAL
    - SCENA17.1.bin (**à éditer avec un éditeur hexadécimal, contient les crédits**)
- DUMP
  - PSX_PAL
    - BINARY
      - AREA030.5.bin.json (**à modifier**)
      - BATE.1.bin.json (**à modifier**)
      - ...
    - dump_enemies.json (**à modifier**)
    - dump_menu.json (**à modifier**)
    - dump_world.json (**à modifier**)
    - pointers_enemies.json (*à ignorer*)
    - pointers_menu.json (*à ignorer*)
    - pointers_world.json (*à ignorer*)

- GFX
  - PSX_PAL
    - AREA016.6.bin.1.8b.64w.64x32.256r.tim (**à modifier**)
    - AREA016.8.bin.4.2.8b.64w.64x32.128r.tim (**à modifier**)
    - ...
- INJECT (*dossier vide à **utiliser pour injecter des fichiers***.)
  - PSX_PAL
    - BEFORE_REINSERT(à remplacer avant le raw reinsert)
      - Vide
    - AFTER_REINSERT (à remplacer après le raw reinsert)
      - Vide
- UNPACKED (*dossier à ignorer*)
  - PSX_PAL
    - BATTLE
    - BMAGIC
    - ...

J'ai mis en évidence toutes les ressources qui devront être modifiées avant que le contenu ne soit réinséré.

> **Attention** : les dossiers `BEFORE_REINSERT` et `AFTER_REINSERT` sont utilisés pour remplacer les fichiers binaires en deux étapes distinctes.
> 
> Tous les fichiers contenus dans le dossier `BEFORE_REINSERT` seront utilisés comme base avant l'opération Raw Reinsert : nous pouvons alors y insérer des fichiers qui ont déjà été édités (par exemple avec des éditeurs hexadécimaux), qui devront à leur tour être manipulés par le processus Raw Reinsert pour insérer du nouveau texte.
> 
> Tous les fichiers contenus dans le dossier `AFTER_REINSERT` remplaceront les précédents créés/édités. En termes simples, ce sont eux qui seront reconditionnés dans les fichiers **EMI** finaux.

## Réinsertion de tous les textes/graphismes/fichiers binaires modifiés
Le référentiel contient également un script `reinsert.sh` qui réintègre tous les contenus précédemment exportés.

### Prérequis
Avant que la réinsertion puisse avoir lieu, nous devons **avoir terminé la procédure de dump** (voir le **chapitre précédent**).

En plus de cela, vous devrez bien sûr avoir modifié les fichiers décrits ci-dessus.

### Réinsertion
Pour réinsérer le contenu, il suffit de lancer l'outil sur le dossier d'origine :

```./reinsert.sh PSX_PAL "--extra-table Ì=9d à=9f è=a0 é=a1 É=a4 ì=a5 ò=a6 ù=a8 È=a9 À=97 Ò=98 Ù=99 …=9b9c °=aa"```

Le script commencera à travailler sur le contenu du dossier modifié en effectuant les étapes suivantes :

1. Supprimer tout dossier temporaire **TEMP**
2. Copier les dumps indexés du dossier **DUMP/platform** dans le dossier **TEMP**.
3. Développer les dumps indexés dans le dossier **TEMP**.
4. Transformer les fichiers dump (**JSON**) du jeu et des menus en fichiers binaires.
5. Copier tous les binaires du dossier **INJECT/platform/BEFORE_REINSERT**.
6. Réinserer manuellement certaines parties des menus dans les fichiers binaires
7. Copier les graphismes **TIM** traduits du dossier **GFX** dans le dossier **TEMP**.
8. Copier et diviser les graphismes **RAW** originaux
9. Convertir les graphismes **TIM** traduits en **RAW**.
10. Remplacer les graphismes **RAW** fractionnés par des graphismes **RAW** traduits.
11. Retirer les graphismes **RAW**.
12. Dupliquer les graphismes **RAW** manquants (beaucoup sont répétés dans plusieurs fichiers)
13. Copier les binaires contenus dans le dossier **BINARY** dans **TEMP**.
14. Dupliquer les fichiers binaires manquants (beaucoup sont répétés dans plusieurs fichiers)
15. Remplacer tous les binaires par ceux qui se trouvent dans le dossier **INJECT/platform/AFTER_REINSERT**.
16. Copier les **EMI** décompressés dans le dossier **UNPACKED** dans **TEMP**.
17. Remplacer les fichiers binaires à reconditionner dans les destinations finales de **TEMP**.
18. Reconditionner tous les fichiers **EMI** dans **TEMP**.
19. Créer le dossier **OUTPUT** final
20. Copier tous les fichiers **EMI** générés dans le dossier **OUTPUT** et, dans le cas de la version **PSP** uniquement, le fichier **BOOT.BIN**.
21. Supprimer tous les fichiers **EMI** (ou le fichier **BOOT.BIN** de la version **PSP**) qui sont encore identiques aux originaux.

### Résultat final
A la fin de toute la procédure, s'il n'y a pas eu d'erreurs, nous obtiendrons dans le dossier **OUTPUT** les fichiers **EMI** (ou le fichier **BOOT.BIN** de la version **PSP**) à réinsérer dans la **ISO** avec cette structure :

- OUTPUT
  - PSX_PAL
    - BATTLE
      - BATTLE.EMI
      - BATTLE2.EMI
      - ...
    - ...
    - WORLD00
      - AREA000.EMI
      - AREA001.EMI
      - ...

> **Attention** : ce n'est que si les **EMI** contiennent des fichiers modifiés qu'ils seront présents dans le dossier **OUTPUT**.

## Ce qui manque

Depuis la **version 1.4.4**, l'analyse des fichiers binaires est terminée, les scripts **dump.sh** et **reinsert.sh** ont été mis à jour pour prendre en charge le **BOOT.BIN** de la version **PSP**. A présent, l'ensemble du texte du jeu est vidable et réinsérable pour les trois versions (**PSX USA**, **PSX PAL** et **PSP**).

Cela signifie qu'il ne manque que la traduction et l'édition graphique :)

Pour des raisons de commodité, j'ai créé un fichier intitulé "Analyse des fichiers.txt" dans lequel figurent des notes sur les différents résultats obtenus pour les trois versions.

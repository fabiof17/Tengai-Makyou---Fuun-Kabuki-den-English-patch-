# Notes techniques : patch anglais de Tengai Makyō: Fūun Kabuki-den

Ce document résume tout ce qu'il faut savoir pour reprendre le travail sur le patch : comment le jeu
stocke son texte, ce que le patch modifie, comment le construire et le tester, et les pièges déjà
rencontrés. Il ne contient aucune donnée du jeu.

Le patch ne modifie que la piste 02 (données) de l'image Redump. La piste 34 est une copie de la
piste 02, mais le jeu ne la lit pas en pratique : il est inutile de la patcher.

## 1. Organisation du disque et de la mémoire

- **Piste 02**, secteurs de 2352 octets ; les données utiles d'un secteur sont aux octets 16 à 2063.
  Le premier secteur de données est le LBA 224 dans la numérotation des outils (`LBA0`).
- **Banques résidentes $68–$73** : secteurs 18 à 65 de la piste, chargés une fois au démarrage
  dans la CD RAM (Super System Card). Elles contiennent le moteur de texte, les menus, les noms,
  les chaînes système. La banque $72 est remise à zéro au démarrage (sauf 0x800–0xBFF) : ne rien
  y stocker.
- **Blocs de terrain** : 0x28000 octets, chargés en $74–$7F + CD RAM $80–$87. Un bloc par zone
  (Kyoto = 642…). Ils commencent tous les 80 secteurs à partir de 242. Ils contiennent les
  dialogues, scripts, objets, et une planche commune de sprites système (voir §6).
- **Scènes cinématiques** : blocs à partir de 4242.
- **Blocs de combat** : 0x10000 octets, chargés en $74–$7B. Il y en a 40 (`battle_bases()`).
  - Le segment de texte 0x20–0x18C4 est **identique dans les 40 blocs** : il est construit une
    seule fois, puis recopié partout.
  - Le texte propre à chaque bloc se trouve vers 0x2D80 et au-delà, à des positions variables.
  - En jeu, le bloc de combat de la zone est mis en cache dans la RAM ADPCM à l'entrée dans la
    zone. Une sauvegarde d'état faite avec une ancienne version garde donc l'ancien texte de
    combat jusqu'au prochain changement de zone.

## 2. Le format du texte

- **Japonais d'origine** : 1 octet pour les kana et kanji courants (table interne), Shift-JIS sur
  2 octets pour le reste. Codes de contrôle 0x00–0x1F (fin, attente, nouvelle ligne, effacement,
  vitesse, INSERT 0x09, PUSH 0x0A, MARK 0x01, police 12 px 0x02 / 8 px 0x03, X/Y/DX/DY, C11, C12,
  C13:n…). Les paramètres numériques sont des chiffres 0xB0–0xB9 (ou ASCII en mode anglais).
  Tout est décodé par `tools/kdtext.py`.
- **INSERT** affiche une chaîne prise dans une table (noms, objets, sorts) ; **PUSH** affiche un
  nombre. Le formateur ($69F8) écrit les chiffres en Shift-JIS pleine largeur (`82 4F+d`, blancs
  `81 40`) dans le tampon $3EEE.
- **Pointeurs** : les messages sont référencés par des pointeurs dans les scripts, souvent au
  milieu d'une suite d'octets. `script.json` (non publié) recense chaque message, ses
  occurrences et ses positions.

## 3. Le moteur anglais (`tools/en_engine.py`)

- **HOOK**, en $DF47 dans la banque $6C (185 octets, il reste environ 1 octet libre), remplace la
  lecture d'un caractère. **TRAMP**, en $6ED3 (85/85 octets, plein), y mène.
- **Mode 8 px** (`$34B3 = 8`, code 0x03) : les octets 0x20–0x7E sont de l'ASCII affiché avec la
  police 8×8. Les codes 0x7F–0xFF sont des mots d'un **dictionnaire** propre à chaque bloc :
  - son en-tête est aux octets 0x1A–0x1C du bloc ;
  - si l'adresse déborde (blocs de boss où 0x1C vaut 0xB7), le moteur retombe sur la banque $74.
- **Codes ajoutés** :
  - 0x1B = saut (JUMP lo hi) vers la suite du message, ailleurs dans le bloc ;
  - 0x1C = fin qui remet la police 12 px (END2) ;
  - 0x1D = remplissage, ignoré par le jeu.
- **Variables** : ACT et DBANK sont stockées dans le HOOK lui-même. Une ancienne version les mettait
  en $25C0, qui est un tampon du jeu : le premier message des scènes disparaissait.
- **Chiffres 8 px** (`build_push`) : le gestionnaire de PUSH ($91C3, banque $6A) appelle une routine
  placée dans la fin libre de la banque $6B. Quand la police est 8 px, cette routine convertit le
  tampon Shift-JIS en ASCII. En 12 px, rien ne change (interface du haut, écran de sauvegarde).

## 4. Construction (`tools/build_en.py`)

- **Entrées** :
  - `disc/orig_track02.bin`, la piste d'origine, jamais modifiée ;
  - `script.json` ;
  - les traductions `en/*.json` (identifiant de message → anglais) ;
  - les fichiers `en/res_*.json` (menus, noms, combat).
- **Commande** : `python3 tools/build_en.py --blocks=$(cat tools/blocks.list)`. Elle écrit
  `disc/kd (Track 02).bin`.
- **Mise en page d'un bloc** (`build_block` / `try_layout`) :
  - le dictionnaire est choisi par bloc, avec plusieurs variantes essayées ;
  - chaque message garde sa position d'origine (les pointeurs ne changent pas) ;
  - un texte trop long laisse en place un début suivi d'un saut 0x1B vers des morceaux placés dans
    la place libérée par d'autres messages ;
  - des messages identiques partagent leur texte (alias) ;
  - chaque message est ensuite **relu par simulation** (`simulate`) : le rapport final donne le
    nombre de messages vérifiés.
- **Combat** (`build_battle`) :
  - le segment commun est construit dans le bloc 4562, puis recopié dans les 40 blocs ;
  - les lignes propres à chaque bloc :
    - `en/res_battle_perblock.json` : lignes présentes dans tous les blocs, mises en page comme
      zones « privées » avec le dictionnaire, puis recopiées dans chaque bloc ;
    - `en/res_battle_enemy.json` : attaques propres à certains monstres, réécrites sur place ;
  - noms dans des zones de 3 octets (Witch, parties du corps des boss) : `TINY_NAMES` (code police
    8 px + un mot du dictionnaire forcé) ;
  - en combat, les messages ne repassent pas en 12 px avant un INSERT : chaque nom fixe lui-même sa
    police.
- **Menus et noms** (`tools/menus_en.py`, `en/res_menus.json`) : les chaînes résidentes réécrites
  sont presque toutes remplacées par un GOSUB (0x1A lo hi) vers un pool. Il reste environ 20 octets
  libres dans ce pool, à surveiller.
- **Empaquetage** : `python3 tools/package.py vX.Y.Z "description"` produit le script PowerShell et
  le xdelta dans `/mnt/user-data/outputs`. Il faut ensuite mettre à jour la version et le MD5 du
  patch dans le README (la deuxième somme MD5 du fichier).

## 5. Tests

- `tools/regress.sh` démarre l'image patchée dans Mednafen sans écran (Xvfb :99) et capture 6 écrans :
  1. le démarrage ;
  2. l'écran de chargement ;
  3. le jeu, avec dialogue et flèche ;
  4. le menu ;
  5. Gear → Equip ;
  6. un combat.

  Il les compare aux références de `tests/ref/`. Code retour 1 en cas de plantage ou de différence.
  `--update` enregistre de nouvelles références.
- Il faut le lancer **après chaque build**, et toujours après une modification de code ou de
  graphismes.
- **Sauvegardes d'état** : les états Mesen fournis (`.mss`) sont convertis pour Mednafen avec
  `tools/mss2mdfn.py`. `tools/res_copy.py` y recopie les banques résidentes patchées. Pour tester
  un texte de combat avec un état ancien, il faut injecter le bloc patché (voir
  `tools/battletest.sh`).
- **Touches de test** (xdotool) : KP_3 = bouton I (valider), KP_2 = bouton II (annuler/menu),
  Return = Run, w/a/s/d = croix.

## 6. Graphismes

- **Barre de surbrillance des menus** : 3 sprites 16×16 (dessin plein) plus un morceau de 12 px de
  large. Ces dessins se trouvent dans la planche de sprites système de chaque bloc de terrain
  (152 copies).
  - Compression par plan de 16×16 : 4 octets de masque (1 = nouvel octet, 0 = répéter le
    précédent), puis les octets.
  - `tools/sprite_gfx.py` réencode `en/gfx/menu_highlight*.png` (PNG indexé 32×32, palette sprite
    0).
- **Piège n° 1** : ces dessins sont lus à la suite. Le dessin réencodé doit garder **exactement**
  sa taille d'origine (71 octets). Le morceau de 12 px partage une zone de 154 octets avec
  d'autres sprites (dont la flèche des dialogues) qui commencent au milieu : seuls ses 20 premiers
  octets peuvent changer (mode `per_pattern`). Tout autre décalage casse la flèche, voire le
  démarrage du jeu (v1.0.34).
- **Position Y de la barre** (menus à MARK) : $6A:0x1F1C, le CLC est remplacé par DEC A (1 px plus
  haut).

## 7. Pièges connus

- **Police dans un GOSUB** : un 0x03 placé dans un sous-programme est annulé au retour. Pour qu'un
  nombre sorte en 8 px, le code 0x03 et le PUSH doivent être dans la même chaîne.
- **Modèles de nombres partagés** : $D407 (3 chiffres : équipement, prix) et $D423 (6 chiffres :
  argent) servent à plusieurs écrans. Leurs blancs de tête effacent les pixels situés à gauche
  (d'où le décalage DX:4).
- **PUSH dans les menus** : `menus_en.py` entoure automatiquement chaque PUSH de
  {FONT12}…{FONT8}, sauf s'il est précédé de {FONT8} ou {FONT12}.
- **Fausses entrées** : des tables de données lues comme du texte se terminent parfois au même
  octet qu'un vrai message. Elles ne doivent pas bloquer la traduction (`REAL_HEADS` liste les
  vrais textes partagés à garder bloquants).
- **Paramètres DY/DX** : leur effet persiste dans les chaînes suivantes. Exemple : le DY:255 de
  l'écran de sauvegarde, à conserver après le nombre.
- **Largeur des fenêtres** : environ 30 caractères par ligne de dialogue, environ 13 dans les
  petites fenêtres de boutique.
- **Sauvegardes d'état anciennes** : elles gardent l'ancien texte de combat (cache ADPCM) et
  l'ancien cache de sprites jusqu'au changement de zone.

## 8. Règles du projet

- Ne jamais publier la ROM ni des données du jeu. `script.json`, les états `.mss` et les captures
  restent hors du dépôt public.
- Après chaque changement : build, `tools/regress.sh`, empaquetage, mise à jour du README, commit,
  push, puis envoi du zip.
- Les messages de commit se terminent par les lignes d'attribution demandées.

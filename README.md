# Tengai Makyō: Fūun Kabuki-den — English patch (v1.0.25)

**[Français](#français) · [English](#english) · [Español](#español)**

---

## Français

### Le jeu

*Tengai Makyō: Fūun Kabuki-den* (天外魔境 風雲カブキ伝) est un jeu de rôle sorti en 1993 sur PC Engine **Super CD-ROM²**. On y incarne Kabuki Danjūrō, l'acteur de kabuki flamboyant de *Tengai Makyō II*, qui part de Kyoto pour Londres afin de vaincre le culte des démons. Le jeu n'est sorti qu'au Japon.

### Ce que fait le patch

Ce patch traduit le jeu du japonais vers l'anglais :

- **Tous les dialogues de l'histoire**, de l'ouverture au théâtre de Kyoto jusqu'à la fin et l'épilogue à Londres.
- **Les cinématiques**, en conservant la vitesse et les pauses du texte d'origine, pour que les phrases restent calées sur les voix et l'animation.
- **Les menus** : menu principal, statut, objets, équipement, arts, réglages, écrans de sauvegarde et de chargement, boutiques, entrepôt, menu de combat, fenêtre de tactique et fenêtre de quantité.
- **Les noms** des objets, équipements, sorts et arts.
- **Le contenu bonus** : le donjon bonus d'Uzume, la maison des Nekodaruma (galerie des scènes, test sonore, liste des musiques, bestiaire) et le mini-jeu de paris.

Pour que l'anglais tienne dans le jeu, le patch ajoute une petite police 8×8 et un moteur de texte anglais avec une compression propre à chaque bloc.

Seule la **piste 02** (la piste de données) est modifiée. Les pistes audio, dont toutes les voix, ne sont pas touchées.

### Ce qu'il vous faut

Une copie du jeu japonais au format **BIN/CUE** (noms Redump) :

| Fichier | MD5 | CRC32 |
|---|---|---|
| `Far East of Eden - Tengai Makyou - Fuun Kabuki-den (Japan) (Track 02).bin` | `ec992a55a340f7d11726d09e3cb7051d` | `21B2006D` |

Il vous faut aussi un émulateur PC Engine CD avec le BIOS Super System Card, par exemple **Mednafen**.

### Comment appliquer le patch

Il y a deux méthodes. Les deux donnent le même résultat. La piste 02 patchée a pour MD5 `ee7364f570f0f81f3a81ded0db24ad11`.

#### Méthode A — script PowerShell (Windows, aucun outil à installer)

1. Copiez `patch/Appliquer_patch_EN-v1.0.25.ps1` dans le dossier qui contient le `.cue` et les `.bin` du jeu.
2. Faites un clic droit sur le script et choisissez **Exécuter avec PowerShell**.
   Si Windows refuse de le lancer, ouvrez les **Propriétés** du fichier, cochez **Débloquer**, validez et réessayez.
3. Le script vérifie la piste d'origine, puis crée deux nouveaux fichiers à côté :
   - `... (Track 02) [EN-v1.0.25].bin`
   - `Far East of Eden - Tengai Makyou - Fuun Kabuki-den (Japan) [EN-v1.0.25].cue`
4. Ouvrez le nouveau fichier `[EN-v1.0.25].cue` dans votre émulateur.

Vos fichiers d'origine ne sont pas modifiés.

#### Méthode B — xdelta (tous systèmes)

1. Appliquez `patch/kabukiden_en_v1.0.25_track02.xdelta` à la **piste 02** d'origine avec [Delta Patcher](https://github.com/marco-calautti/DeltaPatcher) ou `xdelta3` :
   ```
   xdelta3 -d -s "Far East of Eden - Tengai Makyou - Fuun Kabuki-den (Japan) (Track 02).bin" kabukiden_en_v1.0.25_track02.xdelta "Track 02 [EN].bin"
   ```
2. Remplacez la piste 02 d'origine par le fichier patché, ou faites une copie du `.cue` et modifiez sa ligne de la piste 02 pour qu'elle pointe vers le nouveau fichier.

### Limites connues

- Dans les cinématiques, l'anglais doit tenir exactement dans la place du japonais, donc certaines phrases sont courtes ou télégraphiques.
- Dans les dialogues ordinaires, les pauses et changements de vitesse du texte d'origine n'ont pas été conservés. Le texte s'affiche à vitesse normale.
- Quelques fragments de phrases peuvent encore apparaître en japonais.
- Dans la salle du mini-jeu de paris, les noms d'objets du menu des objets peuvent s'afficher en japonais.
- Les écrans de debug des développeurs et le test sonore caché ne sont pas traduits.
- La fenêtre de tactique en combat et certaines scènes de fin de jeu n'ont pas été entièrement testées en conditions réelles. Merci de signaler tout problème.

### Signaler un problème

Ouvrez une *issue* sur ce dépôt. Indiquez la scène ou le lieu, ce que vous voyez (une capture d'écran aide) et ce que vous attendiez.

### Crédits

- Traduction et patch : **fabiof17**, réalisés avec **Claude** (Anthropic).
- *Tengai Makyō: Fūun Kabuki-den* © Hudson Soft / Red Company. Cette traduction de fan est non commerciale. Elle ne contient aucune donnée du jeu : vous devez posséder le jeu.

---

## English

### About the game

*Tengai Makyō: Fūun Kabuki-den* (天外魔境 風雲カブキ伝) is a role-playing game released in 1993 for the PC Engine **Super CD-ROM²**. You play Kabuki Danjūrō, the flamboyant kabuki actor from *Tengai Makyō II*, who travels from Kyoto to London to defeat the Demon Cult. The game was only released in Japan.

### What the patch does

This patch translates the game from Japanese into English:

- **All story dialogue**, from the opening at the theater in Kyoto to the ending and epilogue in London.
- **Cutscenes**, keeping the original text speed and pauses so the lines stay in sync with the voices and animation.
- **Menus**: field menu, status, items, equipment, arts, settings, save and load screens, shops, the storehouse, the battle menu, the tactics window and the item quantity window.
- **Names** of items, equipment, spells and arts.
- **Bonus content**: the Uzume bonus dungeon, the Nekodaruma house (scene gallery, sound test, music list, bestiary) and the gambling mini-game.

To make the English fit, the patch adds a small 8×8 font and an English text engine with per-block compression.

Only **track 02** (the data track) is modified. The audio tracks, including all voice acting, are left untouched.

### Requirements

You need a dump of the Japanese game in **BIN/CUE** format (Redump naming):

| File | MD5 | CRC32 |
|---|---|---|
| `Far East of Eden - Tengai Makyou - Fuun Kabuki-den (Japan) (Track 02).bin` | `ec992a55a340f7d11726d09e3cb7051d` | `21B2006D` |

You also need an emulator that runs PC Engine CD games with a Super System Card BIOS, for example **Mednafen**.

### How to apply the patch

There are two ways. Both produce the same result. The patched track 02 has the MD5 `ee7364f570f0f81f3a81ded0db24ad11`.

#### Option A — PowerShell script (Windows, no extra tool)

1. Copy `patch/Appliquer_patch_EN-v1.0.25.ps1` into the folder that holds the game's `.cue` and `.bin` files.
2. Right-click the script and choose **Run with PowerShell**.
   If Windows refuses to run it, open the file's **Properties**, tick **Unblock**, click OK and try again.
3. The script checks the original track, then creates two new files next to it:
   - `... (Track 02) [EN-v1.0.25].bin`
   - `Far East of Eden - Tengai Makyou - Fuun Kabuki-den (Japan) [EN-v1.0.25].cue`
4. Open the new `[EN-v1.0.25].cue` in your emulator.

Your original files are not modified. The script's messages are in French.

#### Option B — xdelta (any system)

1. Apply `patch/kabukiden_en_v1.0.25_track02.xdelta` to the original **track 02** with [Delta Patcher](https://github.com/marco-calautti/DeltaPatcher) or `xdelta3`:
   ```
   xdelta3 -d -s "Far East of Eden - Tengai Makyou - Fuun Kabuki-den (Japan) (Track 02).bin" kabukiden_en_v1.0.25_track02.xdelta "Track 02 [EN].bin"
   ```
2. Either replace the original track 02 with the patched file, or make a copy of the `.cue` and point its track 02 line at the new file.

### Known limitations

- In cutscenes, the English has to fit in exactly the space of the Japanese text, so some lines are short or terse.
- In regular dialogue, the original text pauses and speed changes were not carried over. The text displays at normal speed.
- A few leftover sentence fragments may still appear in Japanese.
- In the gambling room, item names in the item menu may show in Japanese.
- The developer debug screens and the hidden sound test are not translated.
- The tactics window in battle and some late-game scenes have not been fully tested on real gameplay. Please report any problem.

### Reporting problems

Open an issue on this repository. Include the scene or location, what you see (a screenshot helps) and what you expected.

### Credits

- Translation and patch: **fabiof17**, made with **Claude** (Anthropic).
- *Tengai Makyō: Fūun Kabuki-den* © Hudson Soft / Red Company. This is a non-commercial fan translation. It contains no game data: you must own the game.

---

## Español

### El juego

*Tengai Makyō: Fūun Kabuki-den* (天外魔境 風雲カブキ伝) es un juego de rol publicado en 1993 para PC Engine **Super CD-ROM²**. El jugador controla a Kabuki Danjūrō, el extravagante actor de kabuki de *Tengai Makyō II*, que viaja de Kioto a Londres para derrotar al culto de los demonios. El juego solo salió en Japón.

### Qué hace el parche

Este parche traduce el juego del japonés al inglés:

- **Todos los diálogos de la historia**, desde la apertura en el teatro de Kioto hasta el final y el epílogo en Londres.
- **Las escenas cinemáticas**, conservando la velocidad y las pausas del texto original, para que las frases sigan sincronizadas con las voces y la animación.
- **Los menús**: menú principal, estado, objetos, equipo, artes, ajustes, pantallas de guardar y cargar, tiendas, almacén, menú de combate, ventana de tácticas y ventana de cantidad.
- **Los nombres** de objetos, equipo, hechizos y artes.
- **El contenido extra**: la mazmorra extra de Uzume, la casa de los Nekodaruma (galería de escenas, prueba de sonido, lista de músicas, bestiario) y el minijuego de apuestas.

Para que el inglés quepa en el juego, el parche añade una pequeña fuente de 8×8 y un motor de texto en inglés con compresión propia para cada bloque.

Solo se modifica la **pista 02** (la pista de datos). Las pistas de audio, incluidas todas las voces, no se tocan.

### Requisitos

Una copia del juego japonés en formato **BIN/CUE** (nombres Redump):

| Archivo | MD5 | CRC32 |
|---|---|---|
| `Far East of Eden - Tengai Makyou - Fuun Kabuki-den (Japan) (Track 02).bin` | `ec992a55a340f7d11726d09e3cb7051d` | `21B2006D` |

También necesitas un emulador de PC Engine CD con la BIOS Super System Card, por ejemplo **Mednafen**.

### Cómo aplicar el parche

Hay dos métodos. Ambos dan el mismo resultado. La pista 02 parcheada tiene el MD5 `ee7364f570f0f81f3a81ded0db24ad11`.

#### Método A — script de PowerShell (Windows, sin instalar nada)

1. Copia `patch/Appliquer_patch_EN-v1.0.25.ps1` en la carpeta que contiene el `.cue` y los `.bin` del juego.
2. Haz clic derecho en el script y elige **Ejecutar con PowerShell**.
   Si Windows se niega a ejecutarlo, abre las **Propiedades** del archivo, marca **Desbloquear**, acepta y vuelve a intentarlo.
3. El script comprueba la pista original y crea dos archivos nuevos a su lado:
   - `... (Track 02) [EN-v1.0.25].bin`
   - `Far East of Eden - Tengai Makyou - Fuun Kabuki-den (Japan) [EN-v1.0.25].cue`
4. Abre el nuevo `[EN-v1.0.25].cue` en tu emulador.

Tus archivos originales no se modifican. Los mensajes del script están en francés.

#### Método B — xdelta (cualquier sistema)

1. Aplica `patch/kabukiden_en_v1.0.25_track02.xdelta` a la **pista 02** original con [Delta Patcher](https://github.com/marco-calautti/DeltaPatcher) o `xdelta3`:
   ```
   xdelta3 -d -s "Far East of Eden - Tengai Makyou - Fuun Kabuki-den (Japan) (Track 02).bin" kabukiden_en_v1.0.25_track02.xdelta "Track 02 [EN].bin"
   ```
2. Sustituye la pista 02 original por el archivo parcheado, o haz una copia del `.cue` y cambia su línea de la pista 02 para que apunte al nuevo archivo.

### Limitaciones conocidas

- En las escenas cinemáticas, el inglés tiene que caber exactamente en el espacio del texto japonés, así que algunas frases son cortas o escuetas.
- En los diálogos normales no se conservaron las pausas ni los cambios de velocidad del texto original. El texto se muestra a velocidad normal.
- Algunos fragmentos de frases pueden seguir apareciendo en japonés.
- En la sala del minijuego de apuestas, los nombres de los objetos en el menú de objetos pueden aparecer en japonés.
- No se han traducido las pantallas de depuración de los desarrolladores ni la prueba de sonido oculta.
- La ventana de tácticas en combate y algunas escenas del final del juego no se han probado del todo en partidas reales. Por favor, informa de cualquier problema.

### Informar de un problema

Abre una *issue* en este repositorio. Indica la escena o el lugar, lo que ves (una captura de pantalla ayuda) y lo que esperabas.

### Créditos

- Traducción y parche: **fabiof17**, realizados con **Claude** (Anthropic).
- *Tengai Makyō: Fūun Kabuki-den* © Hudson Soft / Red Company. Esta traducción de fans no es comercial. No contiene datos del juego: debes poseer el juego.

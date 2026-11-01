Premier beta public de **GETSU RAIDER**, une tuile PS5 homebrew (**PPSA99606**)
qui lance **Shadow of the Tomb Raider** avec un runtime Wine bundlé. Pas besoin
d'installer Prospero Win séparément.

## Ce qu'il te faut

- Une PS5 jailbreakée capable d'installer du homebrew.
- **Ta propre copie de Shadow of the Tomb Raider.** Le dossier d'installation
  GOG normal, sans modifications, c'est tout. Le jeu détecte automatiquement
  que Steam n'est pas actif et joue en solo.

## Installation

1. Dézippes `GETSU-RAIDER-PPSA99606.zip` et copies `PPSA99606/` à
   `/data/homebrew/PPSA99606/`, puis installe la tuile.
2. Lance **GETSU RAIDER** une fois : crée `/data/getsu-raider/`.
3. Copies ton dossier de jeu dans `/data/getsu-raider/dropzone/ShadowOfTheTombRaider/`.
4. Ouvre GETSU RAIDER : **✕** pour installer, puis **✕** pour jouer.

Voir le README pour les détails complets, les chemins de sauvegarde et les contrôles.

## Configuration graphique

Le jeu tourne à **2560 × 1440** en **Direct3D 12** (vkd3d-proton), avec
**ray tracing désactivé**. À l'installation, GETSU RAIDER configure les réglages
graphiques de façon prudente (textures medium, filtres plus bas) qui ont été
testés et stables. **Le jeu a été testé en tournant à très haute qualité en 2K** — 
textures, ombres, LOD, occlusion ambiante maxés — **et fonctionne bien pour des
sessions de jeu prolongées**.

Tu peux changer n'importe quel réglage dans le menu options du jeu ; GETSU
n'écrase jamais tes choix.

Le premier chargement après installation peut prendre plusieurs minutes.

## Fichiers

| Fichier | Contenu |
|---------|---------|
| `GETSU-RAIDER-PPSA99606.zip` | Dossier app pour la console |
| `GETSU-RAIDER-PPSA99606-source.zip` | Source complète correspondant |
| `SHA256SUMS` | Checksums des archives |

GETSU RAIDER ne contient aucune donnée de jeu. C'est un fan homebrew, non
affilié à Crystal Dynamics, Eidos-Montréal, Square Enix, Embracer Group ou Sony.
Logiciel libre : voir `THIRD_PARTY.md` et `LICENSE`.

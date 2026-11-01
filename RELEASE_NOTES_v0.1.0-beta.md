First public beta of **GETSU RAIDER**, a PS5 homebrew tile (**PPSA99606**)
that runs **Shadow of the Tomb Raider** with a bundled Wine runtime. No separate
Prospero Win install is needed.

## What you need

- A jailbroken PS5 that can install homebrew.
- **Your own copy of Shadow of the Tomb Raider.** The normal, unmodified GOG
  install folder is all you need. The game auto-detects that Steam is not
  running and plays in single-player.

## Install

1. Unzip `GETSU-RAIDER-PPSA99606.zip` and copy `PPSA99606/` to
   `/data/homebrew/PPSA99606/`, then install the tile.
2. Launch **GETSU RAIDER** once: it creates `/data/getsu-raider/`.
3. Copy your game folder into `/data/getsu-raider/dropzone/ShadowOfTheTombRaider/`.
4. Open GETSU RAIDER: **✕** to install, then **✕** to play.

The README has the details, including save paths and the controls.

## Graphics settings

The game runs at **2560 × 1440** in **Direct3D 12** (vkd3d-proton), with
**ray tracing off**. On install, GETSU RAIDER sets cautious graphics options
(medium textures, lower quality filters) that were tested and stable. **The game
has been tested running at very high quality settings in 2K** — textures, shadows,
LOD, ambient occlusion maxed out — and runs smoothly for extended play sessions.

You can change any setting in the game's options menu; GETSU never overwrites your own choices.

The first load after install may take several minutes.

## Files

| File | What it is |
|------|------------|
| `GETSU-RAIDER-PPSA99606.zip` | The app folder for the console |
| `GETSU-RAIDER-PPSA99606-source.zip` | The complete corresponding source |
| `SHA256SUMS` | Checksums of both archives |

GETSU RAIDER ships no game data. It is a fan homebrew, not affiliated with
Crystal Dynamics, Eidos-Montréal, Square Enix, Embracer Group or Sony. Free
software: see `THIRD_PARTY.md` and `LICENSE`.

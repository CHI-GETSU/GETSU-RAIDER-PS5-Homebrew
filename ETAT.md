# GETSU RAIDER : état de la préparation (2026-11-01)

Title ID **PPSA99606**, nom **GETSU RAIDER**, données sur la PS5 dans `/data/getsu-raider`.

## Fait

- **Sources** (`~/.cache/getsu/raider-src`, copie de celles de SOTF ; celles de SOTF ne sont pas touchées) :
  - menu GETSU adapté à Tomb Raider : `native/getsu_landing.{c,h}`, `native/pw_getsu_front.{c,h}`, `native/wine64_main.c` ;
  - icône de la tuile : `sce_sys/icon0.png`, ton logo vu de dos en 512×512 ;
  - `sce_sys/param.json` : PPSA99606, GETSU RAIDER, `UP9000-PPSA99606_00-GETSURAIDER00001`. Le formulaire ProsperoStore lira donc le bon ID ;
  - `tests/test_title_identity.py` adapté à ce modèle : le test passe.
- **Profil du jeu** (intégré au code) : identique à `/data/prospero-win/profiles/sottr.profile` sur la PS5 (2560×1440, DX12 via vkd3d-proton, `VKD3D_CONFIG=nodxr`, thread_scheduling, fast_clock et shared_input à 1). Seuls les chemins changent.
- **Réglages graphiques prudents** : `runtime/sottr-settings.reg`.
  - Base : tes réglages du 31/10 au soir, lus sur la PS5 dans `sottr-v030/user.reg`.
  - Les niveaux de qualité sont ramenés à ceux du 30/10 : sessions 2K enregistrées sans plantage, jusqu'à 16,6 min.
  - Textures, ombres, LOD et filtrage : 3 → 2. AO et DOF : 2 → 1. Contact shadows : 1 → 0.
  - `FirstRun=0` évite que le jeu redétecte ses réglages au premier lancement.
- **Préfixe Wine** : `dist/PPSA99606/win/prefix-seed`.
  - C'est le préfixe de SOTF, validé avec le runtime GETSU, sans le fichier de réglages de Sons of the Forest et avec les réglages SOTTR ajoutés.
  - Les DLL d3d11, dxgi, d3d12 et d3d12core sont identiques octet par octet à celles du préfixe `sottr-v030` qui a fait tourner le jeu.
- **Test sur PC** (ASan/UBSan) de l'installation : arborescence, détection de `SOTTR.exe`, pose du préfixe, copie du jeu à l'identique, profil écrit.
- **Captures du menu** : `landing/screens/`.
- **Dossier appli** : `dist/PPSA99606/` (3 401 fichiers, 967 Mo), fait à partir de GETSU SOTF : même ntdll avec le fichier d'échange, même libvulkan RADV, mêmes Wine et DXVK.
- **Documentation** :
  - `README.md` ;
  - `dist/PPSA99606/SOURCES.txt` et `THIRD_PARTY.md` ;
  - `source-notes/BUILDING-GETSU-RAIDER.md`.
- **Référence PS5** : `reference-ps5/` contient le profil, le `user.reg` et les derniers logs Prospero, copiés en lecture seule.

## Finalisation locale réalisée

- Helper PPSA99606 compilé depuis le fork Lapy épinglé, sans modification de
  son code ni de ses garde-fous : mode `elf-helper`, une requête, non résident.
- `eboot.bin` compilé avec Clang 18 et la fondation épinglée, puis intégré dans
  `dist/PPSA99606/` avec le helper, son manifeste et sa provenance locale.
- `sce_module/libc.prx` est identique à la copie existante : il a été conservé.
- Wine, RADV, les DLL et le préfixe existants ne sont pas reconstruits.
- Sources exactes Lapy ajoutées à `raider-src/third_party/lapy/` ; logger
  correspondant déjà dans `native/ps5log/`. Les sources RAIDER restent dans
  `~/.cache/getsu/raider-src`, sans création d'un nouveau projet.
- Preuves : `source-notes/BUILD-VERIFICATION.json`.
- Fichiers locaux de test : `GETSU-RAIDER-PPSA99606-TEST-LOCAL.zip`,
  `GETSU-RAIDER-PPSA99606-DEVELOPMENT-SOURCE.zip`, `SHA256SUMS-LOCAL`.
  Leur création n'est pas une publication ni une certification juridique.

## Vérifications locales

- 5 tests Python de manifeste/empreinte : OK.
- Test d'identité du modèle PPSA99606 : OK.
- Tests C du protocole Lapy (transport simulé) et d'accès aux données : OK.
- Manifeste : bon titre, bonnes empreintes ELF/protocole, fonctionnalité
  `root_layout_probe_retry`, `service=false`, `max_requests=1`.
- Aucun `dev.conf` personnel livré. Les journaux et manifestes privés de
  construction restent dans `raider-src/build/raider-finalization/`.

## Reste à faire

1. Test de cette tuile sur PS5, uniquement avec autorisation distincte.
   Aucun accès à la console ni transfert n'a eu lieu pendant cette finalisation.
2. Vérifier en jeu les réglages et le démarrage : les observations précédentes
   concernent la configuration de référence Prospero Win, pas cette tuile.
3. Revue avant publication : sources complètes des dépendances, licences,
   silhouette du logo et chemins personnels hérités du préfixe Wine.
   L'archive de développement n'est pas présentée comme une clôture juridique.
4. Ne pas publier `reference-ps5/` (logs/registre de référence privés).
5. Publication GitHub/ProsperoStore uniquement sur instruction explicite.

GETSU SOTF et les dépendances partagées sont protégés par un inventaire avant/
après. Le rapport de comparaison est `source-notes/PRESERVATION-CHECK.json`.

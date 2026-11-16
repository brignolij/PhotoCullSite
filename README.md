# PhotoCull

Sort your iPhone or Android photos on a Mac or Windows PC: your camera roll grouped by trip, similar shots compared
side by side (up to six at once) and hundreds of photos deleted in a few clicks. PhotoCull also sorts any folder of
photos on your computer, and never modifies your originals. It runs 100% on your devices: no account, no cloud, no upload.

**Website: [photocull.idealy.ch](https://photocull.idealy.ch/)** · [Français](https://photocull.idealy.ch/fr/) · [Italiano](https://photocull.idealy.ch/it/) · [Deutsch](https://photocull.idealy.ch/de/) · [Español](https://photocull.idealy.ch/es/)

- **Mac:** [download PhotoCull for Mac](https://github.com/brignolij/PhotoCullSite/releases/latest/download/PhotoCull-Desktop.zip). Free, macOS 11+ on Apple Silicon, signed and notarized by Apple.
- **Windows:** [get it from the Microsoft Store](https://apps.microsoft.com/detail/9n6wv393b3pl) or [download the zip](https://github.com/brignolij/PhotoCullSite/releases/latest/download/PhotoCull-Windows-x64.zip). Free, Windows 10/11 (64-bit). Reading an iPhone needs the free Apple Devices app from the Microsoft Store. The zip is not code-signed yet, so SmartScreen may warn on first launch.
- **Phones:** an iPhone connects by USB; an Android phone connects over Wi-Fi (Mac and Windows) or by USB (Windows), with the free [TripSweep](https://tripsweep.idealy.ch/) app open on its Deletion tab.
- [FAQ](https://photocull.idealy.ch/faq.html) · [Guides](https://photocull.idealy.ch/blog.html) · [Privacy policy](https://photocull.idealy.ch/privacy.html)

This repository holds the PhotoCull website (published by GitHub Pages) and the
[PhotoCull releases](https://github.com/brignolij/PhotoCullSite/releases). The app's source code is not here.

---

## Maintenance du site

Site statique de PhotoCull seul. TripSweep a son propre site (`brignolij/TripSweepSite`, `tripsweep.idealy.ch`) ;
les liens vers TripSweep sont absolus. Domaine géré chez Infomaniak : voir `DEPLOY.md`.

Tout est généré par `generate.py` dans `sites/web/`, que le workflow `.github/workflows/pages.yml` publie à
chaque push sur `main`. Seule dépendance : `cwebp` (`brew install webp`) pour les captures en WebP.

```
generate.py                   # génère sites/web/ (en = racine, /fr/ /it/ /de/ /es/)
content/{en,fr,it,de,es}.py   # FAQ et articles de blog, par langue
assets/                       # style.css, lightbox.js, logos, captures d'écran (une par langue)
versions.json                 # copie de tripsweep.idealy.ch/versions.json, copiée dans sites/web/
check_links.py                # vérifie liens internes, sitemap, longueur des titres et descriptions
indexnow.py                   # prévient Bing (IndexNow) après une mise en ligne
sites/web/                    # sortie générée et publiée : ne pas éditer à la main
```

Chaque page a ses hreflang, son canonical et ses données JSON-LD ; `sitemap.xml`, `robots.txt`, `llms.txt`,
`404.html`, `CNAME` et les fichiers de vérification (Bing, IndexNow) sont générés aussi. Un nouvel article prend
ses dates dans `POST_DATES` ; titres ≤ 60 caractères, descriptions ≤ 160.

### Mettre à jour

```bash
python3 generate.py
python3 check_links.py https://tripsweep.idealy.ch=../website/sites/web   # l'argument vérifie aussi les liens vers TripSweep
git add -A && git commit && git push
```

Une fois le déploiement Pages terminé :

```bash
python3 indexnow.py
```

### Prévisualiser

```bash
python3 -m http.server 8000 -d sites/web
```

### versions.json : deux copies à garder identiques

Les PhotoCull installés (jusqu'à 1.4.0 au moins, `src/PhotoCull.Core/Updates/UpdateCheck.cs`) lisent
`https://tripsweep.idealy.ch/versions.json` (dépôt TripSweepSite). Ce dépôt en publie une copie sur
`https://photocull.idealy.ch/versions.json` pour qu'une future version de PhotoCull la lise ici. **Tant que des
versions anciennes de PhotoCull tournent, toute modification de `versions.json` se fait dans les deux dépôts, à
l'identique** (même schéma, entrée `desktop` comprise, et les entrées `ios` / `android` restent aussi dans la copie
TripSweep).

### Releases

Les liens « latest » du site pointent sur les releases de **ce** dépôt
(`releases/latest/download/PhotoCull-Desktop.zip` et `PhotoCull-Windows-x64.zip`) : chaque release doit porter
les deux zips. Les anciennes releases de `brignolij/TripSweepSite` restent en place, car `versions.json` (entrée
`desktop.downloads`) et des liens externes y mènent encore.

### Redirections depuis l'ancien site

Les pages PhotoCull étaient sur `tripsweep.idealy.ch` sous un préfixe `desktop`. Le site TripSweep garde à ces
adresses de petites pages de redirection (`meta refresh` + `canonical` + `noindex`, GitHub Pages ne fait pas de
redirection serveur), dans les 5 langues (`/fr/desktop.html` → `https://photocull.idealy.ch/fr/`, etc.) :

| Ancienne URL (`https://tripsweep.idealy.ch/…`) | Nouvelle URL (`https://photocull.idealy.ch/…`) |
|---|---|
| `/desktop.html` | `/` |
| `/desktop-faq.html` | `/faq.html` |
| `/desktop-blog.html` | `/blog.html` |
| `/desktop-blog-sort-iphone-photos-on-mac.html` | `/blog-sort-iphone-photos-on-mac.html` |
| `/desktop-blog-sort-iphone-photos-on-pc.html` | `/blog-sort-iphone-photos-on-pc.html` |
| `/desktop-blog-sort-photos-on-computer.html` | `/blog-sort-photos-on-computer.html` |
| `/desktop-blog-free-up-iphone-from-mac-or-pc.html` | `/blog-free-up-iphone-from-mac-or-pc.html` |
| `/desktop-blog-compare-similar-photos-keep-the-best.html` | `/blog-compare-similar-photos-keep-the-best.html` |
| `/desktop-blog-find-duplicate-photos-on-mac.html` | `/blog-find-duplicate-photos-on-mac.html` |
| `/desktop-blog-find-duplicate-photos-on-windows.html` | `/blog-find-duplicate-photos-on-windows.html` |
| `/desktop-blog-sort-photo-folder-external-drive.html` | `/blog-sort-photo-folder-external-drive.html` |
| `/desktop-blog-sort-android-photos-on-mac.html` | `/blog-sort-android-photos-on-mac.html` |
| `/desktop-blog-sort-android-photos-on-pc.html` | `/blog-sort-android-photos-on-pc.html` |
| `/desktop-blog-free-up-android-from-mac-or-pc.html` | `/blog-free-up-android-from-mac-or-pc.html` |

La politique de confidentialité `https://tripsweep.idealy.ch/privacy.html` n'est pas redirigée : elle reste la page
de TripSweep (et couvre encore PhotoCull pour les versions installées qui y renvoient). PhotoCull a la sienne sur
`/privacy.html` ici.

# Mettre le site en ligne sur pixculler.idealy.ch

Hébergé par GitHub Pages (dépôt `brignolij/PixCullerSite`), domaine `idealy.ch` géré chez Infomaniak.

Ordre à respecter : **retirer la redirection Infomaniak → DNS → GitHub → attendre le HTTPS → Enforce HTTPS →
Search Console / Bing → mettre à jour l'app**.

## 0. Avant : releases

Copier la release `pixculler-1.4.0` (les deux zips `PixCuller-Desktop.zip` et `PixCuller-Windows-x64.zip`) dans ce
dépôt et la marquer « latest », sinon les boutons de téléchargement du site renvoient une 404. Ne pas supprimer les
releases de `brignolij/TripSweepSite` (`versions.json` y renvoie encore).

## 1. Retirer la redirection web existante

`pixculler.idealy.ch` est aujourd'hui une **redirection web Infomaniak (301)** vers
`https://tripsweep.idealy.ch/desktop.html`. Un CNAME ne peut pas coexister avec elle :

1. Manager Infomaniak → **Web & Domaine** → **Domaines** → `idealy.ch` → **Redirections** (ou « Redirection web »).
2. Supprimer la redirection de `pixculler.idealy.ch`.
3. Dans **Zone DNS**, supprimer les enregistrements `A` / `AAAA` / `CNAME` nommés `pixculler` qu'elle aurait créés.

Le site est indisponible entre cette étape et la fin de l'étape 3 (quelques minutes à 1 h). Pendant ce temps,
`tripsweep.idealy.ch/desktop.html` affiche une page « déplacé » qui pointe vers `pixculler.idealy.ch`.

## 2. DNS chez Infomaniak

**Zone DNS** de `idealy.ch` → **Ajouter un enregistrement** :

| Champ | Valeur |
|---|---|
| Type | `CNAME` |
| Source / Nom | `pixculler` |
| Cible | `brignolij.github.io.` (avec le point final si le formulaire le demande) |
| TTL | 3600 (ou la valeur par défaut) |

Ne toucher à aucun autre enregistrement (MX, SPF/TXT : e-mails ; `tripsweep` : l'autre site). Vérifier :

```bash
dig +short CNAME pixculler.idealy.ch   # attendu : brignolij.github.io.
```

## 3. GitHub Pages

1. Pousser ce dépôt : il contient `sites/web/` (avec `CNAME` = `pixculler.idealy.ch`) et le workflow
   `.github/workflows/pages.yml` qui publie ce dossier.
2. Dépôt → **Settings → Pages** :
   - **Source** : `GitHub Actions`
   - **Custom domain** : `pixculler.idealy.ch` → Save (GitHub vérifie le DNS)
3. Attendre la coche verte (jusqu'à ~1 h, le temps que GitHub crée le certificat), puis cocher **Enforce HTTPS**.
4. Tester : https://pixculler.idealy.ch/, https://pixculler.idealy.ch/fr/faq.html,
   https://pixculler.idealy.ch/versions.json, et une ancienne adresse :
   https://tripsweep.idealy.ch/desktop-faq.html doit renvoyer vers https://pixculler.idealy.ch/faq.html.

Optionnel : dans **Settings → Pages**, vérifier le domaine `idealy.ch` au niveau du compte GitHub
(enregistrement TXT `_github-pages-challenge-brignolij`) pour qu'aucun autre compte ne puisse le réclamer.

## 4. Google Search Console et Bing

1. https://search.google.com/search-console → **Ajouter une propriété** → **Préfixe d'URL** →
   `https://pixculler.idealy.ch/`. Vérification par « Balise HTML » : la balise `google-site-verification` du site
   TripSweep est reprise dans toutes les pages (si Google la refuse pour ce domaine, remplacer sa valeur dans
   `seo_meta()` de `generate.py`, ou passer par une propriété « Domaine » `idealy.ch` avec un TXT Infomaniak).
2. **Sitemaps** → ajouter `sitemap.xml`.
3. Dans la propriété `tripsweep.idealy.ch`, rien à faire : les anciennes pages sont en `noindex` et sorties du
   sitemap, elles disparaîtront de l'index d'elles-mêmes. (L'outil « Changement d'adresse » ne sert pas ici : il ne
   s'applique qu'au déménagement d'un site entier.)
4. Bing Webmaster Tools → **Ajouter un site** `https://pixculler.idealy.ch/` (le fichier `BingSiteAuth.xml` est
   publié à la racine ; ou importer depuis Search Console), puis soumettre le sitemap. Ensuite `python3 indexnow.py`.

## 5. Après le HTTPS : à mettre à jour (à demander à Claude)

- `src/PixCuller.App/Views/AboutWindow.cs` : `SiteUrl` → `https://pixculler.idealy.ch/`,
  `PrivacyUrl` → `https://pixculler.idealy.ch/privacy.html`.
- `src/PixCuller.Core/Updates/UpdateCheck.cs` : `ManifestUrl` peut passer à `https://pixculler.idealy.ch/versions.json`
  dans une prochaine version ; garder l'ancien fichier à jour tant que des versions anciennes tournent.
- Scripts de release : publier les zips sur `brignolij/PixCullerSite`.
- Fiche Microsoft Store (site web, politique de confidentialité), `store/listing/certification-notes.md`,
  liens marketing (`companion-app/marketing/*.md`).

## Régénérer le site

```bash
python3 generate.py && python3 check_links.py   # puis commit + push
```

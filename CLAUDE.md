# Caravane Berbère — Documentation Projet

## Infos essentielles
- **Repo GitHub** : https://github.com/ELGUEDDARI/BERBERE-CARAVANE.git
- **GitHub Pages** : https://elgueddari.github.io/BERBERE-CARAVANE/
- **Push** : `git push origin master` — remote configuré avec PAT no-expiration, aucun token à saisir
- **WhatsApp** : +212 662 132 973
- **Serveur preview local** : port 3000 (caravane-berbere)

## Structure des fichiers
```
index.html                    ← Page principale (hero vidéo, galerie, excursions, blog, témoignages)
contact.html                  ← Formulaire de contact
blog-toubkal.html             ← Article Trek Toubkal
blog-merzouga.html            ← Article Merzouga / désert
blog-randonnees.html          ← Article Randonnées Atlas
excursion-haut-atlas.html     ← Page excursion Haut-Atlas
excursion-desert-sud.html     ← Page excursion Désert Sud
excursion-journee.html        ← Page excursions à la journée
excursion-voyages.html        ← Page circuits / voyages
excursion-theme.html          ← Page excursions à thème
excursion-cote-atlantique.html← Page Côte Atlantique
hero-video.mp4                ← Vidéo hero compressée (720x948, 8s, ~1.5 Mo)
```

## Stack technique
- HTML statique + CSS inline + JS vanilla (pas de framework)
- Fonts : Playfair Display (titres) + Inter (corps)
- Pattern zellige SVG généré en JS (arabesque sombre) → appliqué au body via `document.body.style.backgroundImage`
- Photos : fichiers locaux avec fallback Unsplash via `loadBg(id, 'fichier.jpg', 'url-unsplash')`

## Palette CSS (`:root`)
```css
--sand:      #F5EED8   /* fond sable clair */
--dark:      #2E1A0A   /* fond sombre principal (éclairci mai 2026, était #1A1208) */
--dark2:     #38200E   /* (était #201608) */
--orange:    #E8722A   /* CTA, accents */
--orange2:   #D4601E
--teal:      #0D8B7A
--gold:      #D4A438   /* bordures, titres sidebar */
--text-dark: #2A1F0A   /* texte principal (marron foncé) */
--text-muted:#6B5740   /* texte secondaire */
```
- Header : `rgba(46,26,10,0.94)` (mis à jour avec --dark)
- `.dark2` class : `rgba(56,32,14,0.45)` (mis à jour avec --dark2)

## Points critiques à ne pas casser

### Hero (index.html)
- Vidéo dans `#hero-frame` : `border: none` (pas de bordure or visible)
- z-index : `.hero-title { z-index: 6 }`, `#hero-frame { z-index: 3 }`
- Effet rideau CSS : `#hero-title span` avec `letter-spacing` animé au scroll
- Video CSS : `object-fit: cover; object-position: center 30%`
- Mobile hero : `var isMobile = vw < 769; var startW = isMobile ? Math.min(vw*0.82, vw-40) : Math.min(300, vw*0.7)`
- Ligne à NE PAS remettre : `border-width` dans l'animation JS (supprimée car créait des coins visibles)

### Blog pages (blog-*.html)
- Le JS fait `document.body.style.backgroundImage = bb` (zellige sombre)
- **Fix essentiel** : `.art-wrap { background: var(--sand); }` — sans ça, les titres `color: var(--text-dark)` (#2A1F0A) sont invisibles sur fond sombre
- Les `.info-box` ont leur propre `background: var(--dark)` avec texte `var(--sand)` → OK

### Menu hamburger (toutes les pages)
- CSS : `.mob-btn`, `.mob-overlay`, `.mob-nav-links`
- JS : toggle class `.open` sur btn + overlay, gestion `aria-expanded`
- Taille liens menu : `font-size: 1.35rem` (était 2rem — trop grand)
- Visible uniquement `@media (max-width: 768px)`

### Excursions mobile (index.html)
- Desktop : `grid-template-columns: repeat(3,1fr); gap: 2px`
- Mobile : `grid-template-columns: 1fr; gap: 16px; border: none` + `.exc-cat-panel { border-radius: 6px; min-height: 180px }`

### Galerie (index.html)
- Strip défilante : `.photo-strip-track { animation: stripScroll 52s linear infinite }` — 20 items (10 photos × 2 pour loop seamless)
- Mosaïque : `.gallery-mosaic { grid-template-columns: 2fr 1fr 1fr; grid-template-rows: 300px 200px 300px 200px }` — 8 photos avec spanning
- Photos chargées via `loadBg('gm-1', 'fichier.jpg', 'url-unsplash')`

## Photos par page
| Page | Photo hero |
|------|-----------|
| excursion-journee.html | ouzoud-arcs.jpg |
| excursion-voyages.html | ait-benhaddou.jpg |
| excursion-haut-atlas.html | atlas-village.jpg |
| excursion-desert-sud.html | merzouga-dunes.jpg |
| excursion-theme.html | theme-hero.jpg |
| excursion-cote-atlantique.html | essaouira-cote.jpg |

## Vidéo hero
- Source originale : `veo-3.1-fast-audio-1080p_a_ANIME_CETTE_PHOTO_E.mp4` (8.2 Mo, 1080x1920, ~13s)
- Commande ffmpeg de compression :
  ```
  ffmpeg -i [source] -vf "crop=1080:1422:0:248,scale=720:-2" -c:v libx264 -crf 24 -t 8.0 -movflags +faststart -an hero-video.mp4
  ```
- Résultat : `hero-video.mp4` — 720x948px, 8 sec (logo coupé à 9s), ~1.5 Mo, sans audio

## Commandes git utiles
```bash
git add -u
git commit -m "message"
git push origin master
```
(Le remote origin contient déjà le PAT — ne jamais demander le token à l'utilisateur)

## Modifier toutes les sous-pages en lot (PowerShell)
```powershell
$files = Get-ChildItem -Filter '*.html' | Where-Object { $_.Name -ne 'index.html' }
foreach ($f in $files) {
  $c = [System.IO.File]::ReadAllText($f.FullName)
  $c = $c.Replace('ANCIEN TEXTE', 'NOUVEAU TEXTE')
  [System.IO.File]::WriteAllText($f.FullName, $c, [System.Text.Encoding]::UTF8)
}
```
**IMPORTANT** : utiliser `.Replace()` (string simple), jamais `-replace` avec regex multiline → risque de corruption fichier.

## Modifications importantes — Mai 2026

### Section "Mot du Fondateur" (index.html — `#equipe`)
- Ancienne section : grille 4 membres (Mohamed guide, Abdel chef, Driss muletier, Mohamed gérant)
- Nouvelle section : "Mot du Fondateur" — photo Mohamed seul (tp-4) + citation en italique
- CSS : `.founder-wrap`, `.founder-photo`, `.founder-quote`, `.founder-sig`
- JS : seul `loadBg('tp-4', ...)` reste (tp-1, tp-2, tp-3 supprimés)
- Texte fondateur : commence par "Né au cœur de l'Atlas à 2 160 m d'altitude, dans un petit village berbère de la vallée des Aït Bougmaz…"

### Excursions panel "Excursions" (index.html)
- "Dunes et Oasis du Drâa 2 jours" déplacé de "Randonnée dans le Sud" vers "Excursions"
- Ajout : "Le Désert de Merzouga 3 jours" → lien vers `excursion-desert-sud.html`
- Ajout : "Le Désert de Chégaga 3 jours" → lien vers `excursion-desert-sud.html#chegaga`

### Section Chégaga (excursion-desert-sud.html — `#chegaga`)
- Nouvelle section avec itinéraire 3 jours depuis Marrakech → M'Hamid → Erg Chgaga
- Bouton WhatsApp réservation inclus

### Nom supprimé
- "Hassan Aït Ouali" retiré de `blog-toubkal.html` (remplacé par "Mohamed")
- "Hassan (guide montagne)" retiré de `contact.html` (remplacé par Mohamed 34 ans)

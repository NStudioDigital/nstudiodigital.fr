# PROCÉDURE SEO — SITES CLIENTS N/ STUDIO DIGITAL
*Checklist complète à appliquer sur chaque nouveau site, dès la première livraison*

---

## 0. AVANT DE CODER — COLLECTE DES INFORMATIONS

Ces données sont nécessaires pour personnaliser le SEO. À récupérer via le questionnaire client ou l'appel découverte.

- **Nom exact de l'établissement** (tel qu'il apparaît ou apparaîtra sur Google)
- **Adresse complète** (avec arrondissement ou commune)
- **Numéro de téléphone**
- **Activité principale** + spécialités (ex : coiffeur coloriste, head spa rituel japonais, sophrologie enfants…)
- **Zone de chalandise** : arrondissement(s) Lyon ciblé(s), communes proches (Villeurbanne, Caluire, Bron…)
- **URL finale** (nom de domaine choisi)
- **Outil de réservation** utilisé (Planity, Calendly, Doctolib, autre)
- **Mots-clés souhaités** si le client en a exprimé (optionnel)

---

## 1. STRUCTURE HTML — HEAD

### 1.1 Balises obligatoires sur TOUTES les pages

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <!-- CANONICAL -->
  <link rel="canonical" href="https://www.DOMAINE.fr/nom-page.html"/>

  <!-- CHARSET + VIEWPORT -->
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>

  <!-- TITRE : format → [Métier] [Ville] — [Spécialité] · [Nom établissement] -->
  <title>Coiffeur Lyon 6 — Couleur & Coupe · Salon Exemple</title>
  <!-- ⚠️ Max 60 caractères. Doit contenir : métier + ville + mot différenciant -->

  <!-- META DESCRIPTION -->
  <meta name="description" content="[Description 140-160 caractères. Contient : métier, ville, 1-2 services phares, CTA implicite]"/>

  <!-- META KEYWORDS (secondaire mais propre) -->
  <meta name="keywords" content="coiffeur Lyon 6, salon coiffure Lyon, [métier] [arrondissements], [métier] [communes proches]"/>

  <!-- OPEN GRAPH -->
  <meta property="og:title" content="[Même que title, sans le · nom établissement]"/>
  <meta property="og:description" content="[Version courte de la meta description, ~120 car.]"/>
  <meta property="og:image" content="https://www.DOMAINE.fr/og-image.png"/>
  <!-- Image OG : 1200×630px, format PNG, texte lisible, visuellement fort -->
  <meta property="og:image:width" content="1200"/>
  <meta property="og:image:height" content="630"/>
  <meta property="og:url" content="https://www.DOMAINE.fr/nom-page.html"/>
  <meta property="og:type" content="website"/>

  <!-- TWITTER CARD -->
  <meta name="twitter:card" content="summary_large_image"/>
  <meta name="twitter:image" content="https://www.DOMAINE.fr/og-image.png"/>

  <!-- FAVICON -->
  <link rel="icon" type="image/png" href="favicon.png"/>

  <!-- PRELOAD image hero (perf + Core Web Vitals) -->
  <link rel="preload" as="image" href="nomimageHERO.webp" fetchpriority="high"/>
</head>
```

### 1.2 Règles de nommage des titres par page

| Page | Format titre |
|------|-------------|
| Accueil | `[Métier] [Ville] — [Accroche] · [Nom établissement]` |
| Services | `[Service précis] à [Ville] — [Nom établissement]` |
| À propos | `À propos — [Nom établissement] · [Métier] [Ville]` |
| Contact | `Contact & Réservation — [Nom établissement]` |
| Blog article | `[Titre article] — [Nom établissement]` |

---

## 2. STRUCTURE HTML — BODY

### 2.1 H1 — une seule par page, optimisée

```html
<!-- Format recommandé -->
<h1>Coiffeur à Lyon 6 — Couleur, Coupe & Soin</h1>

<!-- ⚠️ Règles :
  - Un seul H1 par page
  - Doit contenir le mot-clé principal (métier + ville)
  - Différent du title (complémentaire, pas copié-collé)
  - 40-70 caractères idéalement
-->
```

### 2.2 Hiérarchie des titres

```
H1 → Sujet principal de la page
  H2 → Sections majeures (Services, À propos, Tarifs, FAQ…)
    H3 → Sous-sections (détail d'un service, sous-catégorie)
```

Pas de H3 sans H2. Pas de H2 sans H1. Pas de saut de niveau.

### 2.3 Contenu textuel minimum par page

- Accueil : 300 mots minimum
- Page service/spécialité : 400-500 mots
- Les mots-clés locaux doivent apparaître **naturellement** dans le texte (pas de bourrage)
- Mentionner la ville/arrondissement au moins 2-3 fois dans les paragraphes
- Chaque page doit avoir un **appel à l'action** visible (bouton réservation, contact, téléphone)

### 2.4 Images — attributs obligatoires

```html
<!-- Toujours renseigner alt, width, height -->
<img
  src="nomimage.webp"
  alt="[Description précise : métier + contexte + localisation si pertinent]"
  width="800"
  height="600"
  loading="lazy"
/>
<!-- Exception hero : loading="eager" ou pas de loading -->

<!-- Exemples alt corrects -->
alt="Soin cheveux en salon de coiffure à Lyon"
alt="Rituel head spa avec huile essentielle, Ko Spa Lyon"
alt="Professeure de pilates reformer au Studio Mova Lyon"

<!-- Exemples alt incorrects -->
alt="image1"
alt="photo"
alt="" <!-- sauf si image purement décorative -->
```

### 2.5 Liens internes

- Chaque page doit pointer vers au moins 2-3 autres pages du site
- La page contact/réservation doit être accessible depuis toutes les pages (nav + CTA body)
- Utiliser des ancres descriptives : `<a href="services.html">Voir nos soins visage</a>` et non `<a href="services.html">Cliquez ici</a>`

---

## 3. SCHEMAS JSON-LD

À placer juste avant `</body>` sur les pages concernées.

### 3.1 LocalBusiness — TOUTES les pages (ou au minimum l'accueil)

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "[TYPE]",
  "name": "[Nom exact de l'établissement]",
  "description": "[Description 1-2 phrases, métier + ville + spécialité]",
  "url": "https://www.DOMAINE.fr",
  "telephone": "+33XXXXXXXXX",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "[Numéro + nom de rue]",
    "addressLocality": "[Ville]",
    "postalCode": "[Code postal]",
    "addressCountry": "FR"
  },
  "geo": {
    "@type": "GeoCoordinates",
    "latitude": XX.XXXXX,
    "longitude": X.XXXXX
  },
  "openingHoursSpecification": [
    {
      "@type": "OpeningHoursSpecification",
      "dayOfWeek": ["Tuesday","Wednesday","Thursday","Friday","Saturday"],
      "opens": "09:00",
      "closes": "19:00"
    }
  ],
  "priceRange": "€€",
  "image": "https://www.DOMAINE.fr/og-image.png",
  "sameAs": [
    "https://www.instagram.com/[compte]",
    "https://www.facebook.com/[page]"
  ]
}
</script>
```

**Types @type selon activité :**

| Activité | @type |
|----------|-------|
| Salon de coiffure | `HairSalon` |
| Head spa / spa | `HealthAndBeautyBusiness` |
| Institut esthétique | `BeautySalon` |
| Nail art | `NailSalon` |
| Massage / bien-être | `HealthAndBeautyBusiness` |
| Sophrologue | `MedicalBusiness` ou `HealthAndBeautyBusiness` |
| Naturopathe | `MedicalBusiness` |
| Yoga / Pilates | `SportsActivityLocation` |

### 3.2 FAQPage — pages avec section FAQ

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "[Question exactement telle qu'elle apparaît visuellement]",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "[Réponse complète, au moins 1-2 phrases]"
      }
    },
    {
      "@type": "Question",
      "name": "...",
      "acceptedAnswer": { "@type": "Answer", "text": "..." }
    }
  ]
}
</script>
```

⚠️ Les questions/réponses du schema **doivent correspondre exactement** au contenu visible sur la page.

### 3.3 Service — pages de services détaillés (optionnel mais utile)

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Service",
  "name": "[Nom du service]",
  "description": "[Description détaillée]",
  "provider": {
    "@type": "[Type établissement]",
    "name": "[Nom établissement]",
    "url": "https://www.DOMAINE.fr"
  },
  "areaServed": {
    "@type": "City",
    "name": "[Ville]"
  },
  "offers": {
    "@type": "Offer",
    "price": "[Prix]",
    "priceCurrency": "EUR"
  }
}
</script>
```

### 3.4 BreadcrumbList — toutes les pages sauf accueil

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    {
      "@type": "ListItem",
      "position": 1,
      "name": "Accueil",
      "item": "https://www.DOMAINE.fr"
    },
    {
      "@type": "ListItem",
      "position": 2,
      "name": "[Nom de la page courante]",
      "item": "https://www.DOMAINE.fr/nom-page.html"
    }
  ]
}
</script>
```

---

## 4. FICHIERS TECHNIQUES

### 4.1 robots.txt (à la racine)

```
User-agent: *
Allow: /

Sitemap: https://www.DOMAINE.fr/sitemap.xml
```

### 4.2 sitemap.xml (à la racine)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://www.DOMAINE.fr/</loc>
    <lastmod>AAAA-MM-JJ</lastmod>
    <changefreq>monthly</changefreq>
    <priority>1.0</priority>
  </url>
  <!-- Une entrée par page indexable -->
  <!-- NE PAS inclure : mentions légales, CGV, pages noindex -->
</urlset>
```

**Pages à inclure dans le sitemap :**
- Accueil (priority 1.0)
- Pages services/spécialités (priority 0.8)
- À propos (priority 0.7)
- Contact (priority 0.7)
- Articles de blog (priority 0.6)
- Portfolio/démos si référencées (priority 0.6)

**Pages à EXCLURE du sitemap (et mettre en noindex) :**
- Mentions légales
- CGV / Politique de confidentialité
- Pages de confirmation / merci
- Pages en cours de construction

### 4.3 Pages noindex

```html
<!-- Dans le <head> des pages à exclure de l'indexation -->
<meta name="robots" content="noindex, nofollow"/>
```

---

## 5. IMAGES — OPTIMISATION TECHNIQUE

### Convention de nommage (OBLIGATOIRE)

```
✅ nomimagesanstiret.webp
❌ nom-image-avec-tiret.webp
❌ NomImage.webp
❌ image1.jpg
```

### Paramètres d'export Canva

- Format : **WebP**
- Qualité : **82**
- Largeur max : **2400px** (images pleine largeur)
- Largeur max : **1200px** (images contenu/card)
- Image OG : **PNG, 1200×630px**

### Performance

- Image hero : `fetchpriority="high"` + `<link rel="preload">` dans le head
- Toutes autres images : `loading="lazy"`
- Toujours renseigner `width` et `height` pour éviter le layout shift (CLS)

---

## 6. MOTS-CLÉS — STRATÉGIE PAR ACTIVITÉ

### Construction du mot-clé principal
`[métier] [ville/arrondissement]`
Exemples : "coiffeur Lyon 6", "head spa Lyon", "sophrologue Villeurbanne"

### Mots-clés longue traîne à intégrer dans le contenu
`[métier] [spécialité] [ville]`
Exemples : "coiffeur curly Lyon", "head spa rituel japonais Lyon", "sophrologie gestion stress Lyon"

### Répartition recommandée par page

| Page | Mot-clé principal | Mots-clés secondaires (dans le corps de texte) |
|------|--------------------|------------------------------------------------|
| Accueil | `[métier] [ville]` | Services phares + ville |
| Service 1 | `[service précis] [ville]` | Variantes + commune voisine |
| Service 2 | `[service précis] [ville]` | Variantes + intention (prix, avis…) |
| À propos | `[métier] [ville] [prénom/nom]` | Histoire + valeurs |
| Contact | `[métier] [ville] réservation` | Téléphone, adresse, horaires |

---

## 7. COHÉRENCE NAP (Name / Address / Phone)

**Le nom, l'adresse et le téléphone doivent être IDENTIQUES sur :**
- Le site (footer, page contact, schema LocalBusiness)
- La fiche Google Business
- Les réseaux sociaux

Format recommandé pour le footer :
```
[Nom établissement]
[Adresse complète]
[Téléphone au format 06 XX XX XX XX]
[Email]
```

---

## 8. CHECKLIST LIVRAISON — À VALIDER PAGE PAR PAGE

### Pour chaque page :

- [ ] `<title>` unique, 50-60 car., contient métier + ville
- [ ] `<meta name="description">` unique, 140-160 car.
- [ ] `<meta name="keywords">` renseignée
- [ ] `<link rel="canonical">` correct (URL exacte de la page)
- [ ] Open Graph complet (title, description, image, url, type)
- [ ] Twitter card renseignée
- [ ] Favicon présente
- [ ] Un seul H1, contient le mot-clé principal
- [ ] Hiérarchie H2/H3 logique et sans saut de niveau
- [ ] Contenu texte minimum (300 mots accueil, 400 pages services)
- [ ] Toutes les images ont un attribut `alt` descriptif
- [ ] Toutes les images ont `width` et `height`
- [ ] Image hero : `loading="eager"` et `<link rel="preload">`
- [ ] Images corps : `loading="lazy"`
- [ ] Liens internes vers 2-3 autres pages
- [ ] CTA réservation/contact visible
- [ ] Schema JSON-LD LocalBusiness présent (accueil)
- [ ] Schema FAQPage si FAQ présente
- [ ] Schema BreadcrumbList (toutes pages sauf accueil)
- [ ] Meta description : accroche émotionnelle + argument clé + CTA ("Devis gratuit", "Sur rendez-vous")
- [ ] Prix en description : utiliser "À partir de [prix]" plutôt qu'un tarif fixe

### Pour le site entier :

- [ ] `robots.txt` créé et accessible
- [ ] `sitemap.xml` créé, toutes pages indexables incluses
- [ ] Pages légales en `noindex`
- [ ] NAP cohérent site + Google Business
- [ ] Images nommées sans tirets, format WebP
- [ ] Images OG présentes sur toutes les pages (PNG 1200×630)
- [ ] Site testé mobile (Chrome DevTools, vrai smartphone)
- [ ] Temps de chargement < 3s (tester sur PageSpeed Insights)
- [ ] `lastmod` du sitemap.xml mis à jour après chaque modification de page

---

## 9. APRÈS LA MISE EN LIGNE

### J+0 — Soumettre à Google Search Console

1. Ajouter la propriété (type "Préfixe d'URL" ou "Domaine")
2. Vérifier via le fichier HTML ou le DNS
3. Soumettre le sitemap : Indexation > Sitemaps > coller l'URL du sitemap
4. Demander l'indexation manuelle des pages principales
   - Inspection d'URL > Demander l'indexation
   - **Quota : 10 demandes maximum par jour**, renouvelé chaque matin
   - **Ordre de priorité :** accueil → pages secteurs → blog → portfolio

### J+7 — Vérifications

- [ ] Sitemap lu sans erreur (Search Console)
- [ ] Pages en cours d'indexation
- [ ] Aucune erreur de couverture bloquante

### J+14 à J+30 — Vérifications rich snippets

⚠️ Attendre **2 à 4 semaines** après indexation avant de vérifier l'apparition des rich snippets FAQ — Google prend du temps pour les traiter même si le schema est valide.

- [ ] Tester les schemas sur [search.google.com/test/rich-results](https://search.google.com/test/rich-results)
- [ ] Vérifier l'apparition des FAQ enrichies dans les SERP
- [ ] Vérifier qu'aucune page importante n'est en "Exclu"

### Conseil client à donner

> Donnez à votre client ces 3 actions à faire dès la mise en ligne :
> 1. Créer / compléter sa fiche Google Business (si pas encore fait)
> 2. Demander des avis Google à ses premiers clients (lien direct à leur envoyer)
> 3. Partager l'URL du site sur ses réseaux sociaux et à ses contacts

---

## 10. NOTES SPÉCIFIQUES PAR ACTIVITÉ

### Coiffeur
- H1 recommandé : `Coiffeur à [Ville] — [Spécialité 1] & [Spécialité 2]`
- Services à décrire séparément : coupe, couleur, balayage, soins, curly/bouclé
- Mot-clé fort : "coiffeur [arrondissement]" + "salon de coiffure [ville]"
- Option galerie avant/après = fort levier de conversion

### Head Spa
- H1 : `Head Spa à Lyon — [Nom du rituel phare]`
- Décrire chaque rituel séparément (durée, bienfaits, produits)
- Mettre en avant l'univers et l'ambiance (différenciant fort)
- Mot-clé : "head spa Lyon" (peu concurrencé, forte demande actuelle)

### Institut esthétique / Nail art
- H1 : `Institut de beauté à [Ville] — [Services phares]`
- Lister les soins avec prix si possible (favorise les clics)
- Mots-clés : "soin visage [ville]", "nail art [ville]", "épilation [ville]"

### Massage / Bien-être
- H1 : `Massage [type] à [Ville] — [Nom praticien ou établissement]`
- Différencier les types de massage sur des pages ou sections distinctes
- Mots-clés : "massage [type] [ville]", "massage bien-être [commune]"

### Sophrologue / Naturopathe
- H1 : `Sophrologue à [Ville] — [Spécialité : stress, enfants, sommeil…]`
- Créer du contenu autour des problématiques clients (pas juste la méthode)
- Mots-clés : "[métier] [ville]", "[métier] [problématique] [ville]"

### Yoga / Pilates
- H1 : `Studio [Yoga/Pilates] à [Ville] — [Type de cours]`
- Décrire les cours + niveaux + horaires
- Mots-clés : "yoga [ville]", "pilates reformer [ville]", "cours [discipline] [arrondissement]"

### Spa / Institut
- H1 : `Spa à [Ville] — [Soins phares]`
- Mettre en avant les soins signatures et l'expérience globale
- Mots-clés : "spa [ville]", "soin corps [ville]", "hammam [ville]"

---

---

## 11. FICHE GOOGLE BUSINESS PROFILE

### Vérification du compte

- La vérification est **obligatoire** avant toute visibilité sur Google Maps
- Deux méthodes : **courrier postal** (carte avec code, délai 1-2 semaines) ou **vidéo** (vérification en direct via l'appli Google Maps)
- Si le courrier n'arrive pas au bout de 2 semaines : **basculer immédiatement sur la vérification vidéo** (disponible dans les paramètres du profil)

### NAP — Cohérence obligatoire

Le nom, l'adresse et le téléphone doivent être **exactement identiques** sur :
- Le site web (footer, page contact, schema LocalBusiness)
- La fiche Google Business
- Les réseaux sociaux (Instagram, Facebook…)

Toute divergence nuit au SEO local et peut bloquer la vérification.

### Photos à ajouter sur la fiche

- Logo de l'établissement
- Photo de couverture (même visuel que le hero du site si possible)
- Photos de l'intérieur (ambiance, espace de soin)
- Photos des prestations / résultats (avec accord client)
- Photo de façade / extérieur (aide les clients à trouver l'adresse)

### Avis Google — Actions à donner au client

> À donner au client dès la mise en ligne :
> 1. Générer son **lien direct d'avis** : fiche Google Business > Obtenir plus d'avis > copier le lien
> 2. Envoyer ce lien à ses premiers clients par SMS ou message direct
> 3. L'ajouter dans sa signature email et en story Instagram
>
> Les premiers avis sont déterminants pour la confiance et le classement local — ne pas attendre.

---

*Procédure N/ Studio Digital — Version 1.1 — Juin 2026*
*À mettre à jour au fil des retours terrain et des évolutions Google*

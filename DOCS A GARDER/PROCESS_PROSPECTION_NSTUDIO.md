# Pipeline de prospection automatisée — Adapté pour N/ Studio Digital

> Document de passation. Reprend le pipeline n8n d'origine (UpLift / artisans BTP)
> et l'adapte à **N/ Studio Digital** (Nadège Dufour) : sites vitrines sur-mesure
> pour les pros **beauté & bien-être**, métropole de Lyon.
>
> Stack : **n8n (Antigravity, cloud 2.11.3)** · LLM **Google Gemini** (nœud AI Agent natif)
> · CRM **Google Sheets** · envoi email **SMTP**.

---

## 0. Ce qui change par rapport au pipeline d'origine

| Élément | UpLift (origine) | N/ Studio Digital (adapté) |
|---|---|---|
| Cible | Artisans BTP | Pros beauté/bien-être |
| Source de leads | API ADEME RGE | **Google Maps / Places API** |
| Zone | Dépts 89 / 77 | **Rhône 69 + limitrophes 01 / 38 / 42** |
| Cible retenue | Sans site + avec email | **Avec site (daté / Wix / Planity / sans réservation)** |
| Offre vendue | Site 1 500-3 000 €, livré 48h | **Site 990 € TTC, livré 3 semaines** |
| Différenciateur | « livré en 48h » | **spécialiste beauté · propriété totale (domaine/code) · réservation intégrée · SEO local** |

**Pourquoi changer la source ?** Les métiers beauté/bien-être ne sont pas dans la base
RGE (réservée au BTP certifié). En revanche ils vivent sur **Google Maps** (avis, fiche,
réservation). Places API donne : nom, adresse, **téléphone, site web (ou son absence),
note Google, nombre d'avis** — exactement les signaux de qualification utiles ici.

**Décision de cadrage (démarrage) :** on ne traite **que les prospects AVEC un site web**
(email récupérable en scrapant le site). Les pros sans site (souvent juste sur Instagram)
ne sont pas joignables par email auto → on les ignore pour l'instant (canal
téléphone/Instagram à ajouter plus tard).

---

## 1. Architecture globale

```
1.  Sourcing Google Maps   →  2. Qualification IA  →  4. Cold email IA
      →  5. Relances J+3 / J+7 / J+14  →  6. Monitoring réponses  →  7. Rapport quotidien
```

Modules réutilisables **tels quels** (changer seulement les prompts/textes) : 2, 4, 5, 6, 7.
Module **à refondre** : le sourcing (1).

---

## 2. MODULE 1 — Sourcing Google Maps  *(le gros du travail d'adaptation)*

Réutiliser le workflow existant **`module1_scraping_google_maps.json`**. Il fait déjà :
`Lire Requêtes (Sheet) → Places Text Search → Place Details → Formater → Sauvegarder Leads`.

### 2.1 Clé API à créer
1. https://console.cloud.google.com → nouveau projet.
2. Activer **Places API**.
3. Créer une clé API, la restreindre à Places API.
4. Crédit gratuit Google ≈ 200 $/mois (≈ 10 000 recherches).
5. Mettre la clé **en dur** dans les nœuds HTTP Request (mode « Fixed » — voir contraintes §8).

### 2.2 ⚠️ 3 corrections à appliquer au JSON avant import
1. **Remplacer les nœuds `Filter`** (« Filtrer en_attente », « Résultats trouvés? ») par des
   **Code nodes** — les Filter plantent sur Antigravity 2.11.3 (bug caseSensitive). Cf §8.
2. **Ajouter un scraping d'email** : Places API ne renvoie **pas** l'email. Après
   « Google Place Details », pour les leads ayant un `website`, ajouter :
   - un **HTTP Request** (GET) qui récupère le HTML du site,
   - un **Code node** qui extrait l'email via regex `[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,}`
     (prioriser les `mailto:`, ignorer les emails de plateformes type `@sentry`, `@wixpress`).
   - Si aucun email trouvé → statut `a_contacter_autrement` (sorti du flux email auto).
3. **`Execute Once`** sur le nœud Google Sheets « Lire Requêtes » (sinon il lit N fois).

### 2.3 Onglet « Requêtes » — métiers × villes (liste de démarrage)

**Métiers beauté/bien-être à scraper :**
`salon de coiffure`, `coiffeur`, `barbier`, `institut de beauté`, `esthéticienne`,
`onglerie`, `prothésiste ongulaire`, `head spa`, `institut de massage`,
`massage bien-être`, `spa`, `sophrologue`, `naturopathe`, `réflexologue`,
`kinésithérapeute`, `studio yoga`, `pilates`.

**Villes (69 + limitrophes) :**

| departement | villes |
|---|---|
| 69 (Rhône / Grand Lyon) | Lyon (1er→9e), Villeurbanne, Caluire-et-Cuire, Bron, Tassin-la-Demi-Lune, Rillieux-la-Pape, Vénissieux, Vaulx-en-Velin, Écully, Sainte-Foy-lès-Lyon, Oullins, Saint-Priest, Meyzieu, Décines-Charpieu, Craponne, Francheville, Villefranche-sur-Saône |
| 01 (Ain) | Miribel, Beynost, Montluel, Dagneux |
| 38 (Isère) | Bourgoin-Jallieu, L'Isle-d'Abeau, Vienne |
| 42 (Loire) | Saint-Étienne, Saint-Chamond |

> Chaque ligne du Sheet = `requete` (métier) + `ville` + `departement` + `statut_scraping = en_attente`.
> Astuce : démarrer **Grand Lyon (69) d'abord** (cœur de cible + SEO local de Nadège :
> « coiffeuse Lyon 6 », etc.), puis étendre aux limitrophes une fois le 69 traité.
> ~17 métiers × ~25 villes ≈ 400 requêtes → laisser tourner par lots.

---

## 3. MODULE 2 — Qualification IA

Gemini **2.5 Flash** analyse le site existant du prospect et sort un score + un angle.
Ici, comme on cible des pros **qui ont déjà un site**, l'angle = **refonte**.

### Critères « lead chaud » pour cette niche
- **Beaucoup d'avis Google + bonne note** mais **site faible** → priorité maximale
  (la demande existe, le site ne convertit pas).
- Site **non responsive** / lent / daté.
- Site sur **CMS tiers** (Wix, Wordpress, **Planity**, **Treatwell**) → différenciateur fort
  de Nadège : « propriété totale, pas de dépendance plateforme ».
- **Pas de réservation en ligne** intégrée → argument direct (Nadège l'intègre).
- Pas de référencement local visible.

### Prompt (adapté de `prompts/analyseur_site_web.md`)
```
Tu es un expert en web design et en acquisition locale pour les métiers de la beauté
et du bien-être. Analyse le HTML de ce site et évalue sa qualité pour un institut /
salon / praticien.

CRITÈRES :
1. Mobile responsive ? (viewport, media queries)
2. HTTPS ?
3. Réservation en ligne présente ? (lien Planity, Treatwell, formulaire, bouton réserver)
4. Plateforme utilisée ? (Wix, WordPress, Planity, Treatwell, fait main…)
5. Design moderne ou daté ? Photos pro ou amateur ?
6. SEO local visible ? (ville dans les titres, fiche Google liée)
7. Contenu suffisant (prestations, tarifs, à propos) ?

RÉPONDS EN JSON :
{
  "score_qualite": 0-100,
  "mobile_responsive": true/false,
  "reservation_en_ligne": true/false,
  "plateforme": "Wix | WordPress | Planity | Treatwell | fait main | inconnu",
  "design_moderne": true/false,
  "obsolete": true/false,
  "problemes_principaux": ["...", "..."],
  "angle_prospection": "Phrase d'accroche personnalisée centrée sur SON salon"
}
```
> `score_lead` retenu = combine score site (plus il est BAS, mieux c'est pour prospecter)
> + nombre d'avis Google (plus il est HAUT, plus la demande est là). Cible idéale :
> **beaucoup d'avis + site faible**.

---

## 4. MODULE 4 — Cold email personnalisé

Gemini **3.1 Pro** rédige. Mêmes règles de copywriting que l'original
(`prompts/cold_email_writer.md`) — seuls l'identité, l'offre et l'angle changent.

### Prompt adapté (à mettre dans le nœud AI Agent)
```
Tu es Nadège Dufour, fondatrice de N/ Studio Digital. Tu crées des sites vitrines
sur-mesure pour les professionnels de la beauté et du bien-être (salons de coiffure,
instituts, head spa, esthétique, sophro, naturo, yoga…) dans la région lyonnaise.

PROFIL PROSPECT :
- Établissement : {nom_entreprise}
- Métier : {categorie}
- Ville : {ville}
- Note Google : {note_google} ({nb_avis} avis)
- Site actuel : {site_web}
- Angle : {angle_prospection}

STRUCTURE (60-100 mots max, plain text) :
a) Observation personnalisée sur EUX (ex : leurs {nb_avis} avis, leur ville)
b) Problème concret (site daté / pas de réservation en ligne / dépendance à une plateforme)
c) Preuve rapide (un site beauté sur-mesure qui convertit les visiteurs en RDV)
d) CTA = question fermée (ex : "Je peux vous montrer une maquette, ça vous dit ?")

ARGUMENTS À PLACER SELON LE PROFIL (1 seul, le plus pertinent) :
- Spécialiste beauté/bien-être (pas un généraliste)
- Site 100% à vous : domaine, code et contenu vous appartiennent (≠ Wix/Planity)
- Réservation en ligne intégrée
- SEO local ("{categorie} {ville}") pensé dès la conception
- 990 € tout compris, livré en 3 semaines

OBJET : minuscules, 3-5 mots, curiosité (ex : "votre salon à {ville}")

SIGNATURE :
Nadège — N/ Studio Digital
Sites web pour la beauté & le bien-être · {telephone_nadege}

FOOTER :
PS : si ce n'est pas le bon moment, dites-le moi et je ne vous recontacterai plus.

RETOURNE EN JSON : {"objet": "...", "corps": "..."}
```

### Règles de délivrabilité (inchangées)
- Plain text, pas de lien unsubscribe, pas de mots spam (gratuit, promo, urgent…).
- Objet du mail : rotation aléatoire parmi ~6 formules.
- **Délai aléatoire 1-4 min** entre chaque envoi.
- 30-50 emails/jour max, warm-up 2-4 semaines.
- Horaires : **beauté = différent du BTP**. Privilégier **lundi (jour de fermeture
  fréquent des salons), tôt le matin (8h-9h30) ou en soirée (19h-21h)**. Éviter mer/sam (rush).

---

## 5. MODULES 5 / 6 / 7 — Suivi *(réutilisables tels quels)*

| Module | Rôle | Schedule | À adapter |
|---|---|---|---|
| **5. Relances** | J+3 courte → J+7 mini-audit gratuit → J+14 dernière | 9h, lun-ven | Signature Nadège + offre 990€/3 sem. dans les prompts (`prompts/relances.md`) |
| **6. Monitoring** | Lit la boîte mail, détecte réponses + STOP/désinscription | toutes les 30 min | Connecter la boîte mail de Nadège |
| **7. Rapport** | Stats du jour par email | 8h chaque jour | Email destinataire = Nadège |

Idée relance J+7 spécifique beauté : proposer un **mini-constat gratuit** (« j'ai regardé
votre fiche Google et votre site, voici 2 choses qui vous font perdre des RDV »).

---

## 6. CRM — Google Sheets (3 onglets)

Repartir du template d'origine (`docs/google_sheets_template.md`). Ajustements :

- **Onglet Requêtes** : colonnes `requete` / `ville` / `departement` / `statut_scraping`.
  Pré-remplir avec les métiers × villes du §2.3.
- **Onglet Leads** : ajouter / utiliser les colonnes utiles ici →
  `note_google`, `nb_avis`, `reservation_en_ligne`, `plateforme`, `score_lead`,
  `angle_prospection`, `google_maps_url`, `statut`.
  Statuts : `nouveau → qualifié → email_envoyé → relance_1/2/3 → répondu → rdv → converti / perdu / désinscrit / a_contacter_autrement`.
- **Onglet Stats** : formules de suivi (taux réponse, conversion, RDV, désinscrits).

---

## 7. Délivrabilité & RGPD (inchangé)

- **Domaine email dédié** (pas le principal) + **SPF / DKIM / DMARC**. Cf `docs/email_domain_setup.md`.
- Warm-up progressif : 5 → 10 → 20 → 30-50 emails/jour sur 2-4 semaines.
- Bounce < 3-5 % (retirer immédiatement un email qui bounce).
- RGPD B2B France : email **professionnel** sans consentement préalable = OK ; opt-out par
  réponse ; tenir une **liste STOP** ; supprimer les données sous 30 jours si demandé.
  ⚠️ Ne PAS cibler d'adresses perso `@gmail.com` / `@orange.fr` d'un particulier.

---

## 8. ⚠️ Contraintes techniques n8n Antigravity (2.11.3) — à connaître AVANT de construire

Ces points font perdre des heures de debug. À appliquer dès le départ :

1. **Pas de nœuds IF / Switch / Filter** (bug caseSensitive qui crash) → toute la logique
   conditionnelle en **Code nodes**. *(C'est pourquoi il faut corriger le Module 1 — §2.2.)*
2. **`$env` interdit** → clés API en dur dans les nœuds, en mode **« Fixed »** (pas Expression).
3. **`fetch()` indisponible** dans les Code nodes → utiliser les **HTTP Request nodes** natifs.
4. **Backticks instables** dans les Code nodes → `var` + concaténation avec `+` et **guillemets
   simples uniquement** (les accents/tirets longs dans les strings cassent aussi).
5. **Google Sheets Read** : cocher **« Execute Once »**, sinon il lit le Sheet N fois.
   Le filtre natif du nœud Read est peu fiable → lire TOUT puis filtrer dans un Code node.
6. **Signature SMTP** non ajoutée automatiquement → l'injecter dans le Code node avant l'envoi.
7. **Pas de champ « From Name »** → utiliser le format
   `Nadège - N/ Studio Digital <adresse@domaine.fr>` dans le champ From Email.
8. **LLM = Gemini via nœud AI Agent natif** (pas HTTP Request, sinon stream au lieu de JSON).
   `gemini-2.0-flash` est **obsolète** (404). Utiliser `gemini-3.1-pro` (rédaction) et
   `gemini-2.5-flash` (analyse/parsing).
9. Écrire le code JS dans des **fichiers `.js`** et le copier depuis le fichier (pas depuis le
   chat — les guillemets se transforment).

---

## 9. Ordre de déploiement conseillé

| Étape | Action |
|---|---|
| 1 | Créer clé Places API + Google Sheets (3 onglets) + remplir Requêtes (Grand Lyon d'abord) |
| 2 | Importer + corriger Module 1 (Filter→Code, ajouter scraping email) → tester sur 3 requêtes |
| 3 | Module 2 — vérifier scores/angles sur 10 leads réels |
| 4 | Module 4 — **s'envoyer le cold email à soi-même** avant tout envoi réel |
| 5 | Configurer domaine dédié + warm-up (en parallèle dès le début) |
| 6 | Modules 5 / 6 / 7 |
| 7 | Test end-to-end, puis lancement en volume progressif |

---

## 10. Fichiers de référence (dossier d'origine)

- `workflows/module1_scraping_google_maps.json` — sourcing (à corriger, §2.2)
- `workflows/module2_filtrage_qualification.json` — qualification
- `workflows/module4_cold_email.json` — cold email
- `workflows/module5_relances.json` / `module6_monitoring_reponses.json` / `module7_rapport_quotidien.json`
- `scripts/parser_email_code.js` — parser du JSON Gemini + signature (adapter la signature → Nadège)
- `prompts/` — tous les prompts d'origine
- `docs/email_domain_setup.md`, `docs/google_sheets_template.md`, `docs/setup_guide.md`

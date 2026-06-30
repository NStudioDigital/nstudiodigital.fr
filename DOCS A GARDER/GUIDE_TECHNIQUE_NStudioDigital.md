# Guide Technique — N' Studio Digital
**Dernière mise à jour : 26 juin 2026**

---

## Stack technique

| Élément | Détail |
|---|---|
| Type de site | HTML statique (pas de WordPress, pas de base de données) |
| Hébergement | Vercel |
| Dépôt GitHub | NStudioDigital/nstudiodigital.fr |
| Dossier racine Vercel | SITE/fichiers-html |
| Dossier local | Desktop/N STUDIO DIGITAL/SITE/fichiers-html |
| Nombre de pages | 24 fichiers HTML (18 pages + 6 articles de blog) |

---

## Outils d'analyse installés

### Microsoft Clarity
- **ID de projet :** xd0kyqw9c7
- **Tableau de bord :** clarity.microsoft.com
- **Installé le :** 26 juin 2026
- **Ce que ça mesure :** enregistrements de sessions, heatmaps, rage clicks

### Google Analytics 4
- **ID de mesure :** G-W30NKY3RZW
- **ID de flux :** 15155784874
- **Tableau de bord :** analytics.google.com
- **Installé le :** 26 juin 2026
- **Ce que ça mesure :** trafic, sources de visites, leads (formulaire de contact)

---

## Workflow de mise en ligne

Pour toute modification de fichier HTML/CSS :

```bash
git add fichiers-html/*.html fichiers-html/blog/*.html
git commit -m "Description de la modification"
git push
```

Vercel déploie automatiquement après chaque push sur `main`.

**Ne jamais faire `git add .`** — risque d'envoyer les dossiers sensibles sur GitHub (PROSPECTION/, DEMOS SITES PROSPECTS/, LINKEDIN/, etc.).

---

## Règles importantes pour Claude Code

- Site statique HTML/CSS uniquement — pas de WordPress, pas de PHP, pas de base de données
- Pas de Local.app — les fichiers sont édités directement
- Le déploiement se fait via git push → Vercel déploie automatiquement
- Toujours committer uniquement les fichiers HTML/CSS, jamais les dossiers métier
- Les scripts d'analyse (GA4 + Clarity) sont déjà présents dans le `<head>` de tous les fichiers — ne pas les dupliquer

# Design — /prospect CRM Prospection Tool
*2026-03-12*

## Objectif

Outil de prospection pour OHOS : ajouter des leads agents immobiliers et auditer leurs sites web depuis Claude Code, avec stockage structuré dans Notion.

## Architecture choisie

**Approche B — Skill Claude Code + Notion MCP**

```
Claude Code (/prospect)
    ├── LinkedIn URL → parse profil via Chrome MCP → crée Lead + Agence dans Notion
    ├── Website URL → fetch via Chrome MCP → audit UX/design → met à jour Agence
    └── Screenshot path → Claude Vision → analyse problèmes → met à jour Agence
```

Pourquoi pas une extension Chrome : Claude Code est déjà un chatbot avec accès browser (Chrome MCP) et Notion (Notion MCP). Le skill standardise le workflow sans développement supplémentaire.

## Notion — Structure CRM

**Page racine :** OHOS Prospection (`321796ad-7073-8161-9f81-f9732225411c`)

### Database Agences (`collection://097c637d-4ffd-492a-9a54-9902d81f3279`)

| Champ | Type | Usage |
|-------|------|-------|
| Nom | Title | Nom de l'agence |
| Site web | URL | URL du site |
| Ville | Select | Nice, Cannes, Antibes, Monaco, Mougins, Saint-Tropez, Lyon, Bordeaux, Pays Basque |
| Audit site | Rich text | Résultats d'audit datés (append) |
| Score site | Select | Bon / Moyen / Mauvais |
| Nombre de leads | Number | Compteur |
| Notes | Rich text | Notes libres |
| Leads | Relation | ← lien bidirectionnel depuis Leads |

### Database Leads (`collection://a5478c7d-401a-4061-8107-3593276c1086`)

| Champ | Type | Usage |
|-------|------|-------|
| Nom | Title | Nom complet |
| Agence | Relation | → vers Agences |
| LinkedIn | URL | Profil LinkedIn |
| Poste | Rich text | Titre / fonction |
| Email | Email | Email pro |
| Téléphone | Phone | Numéro direct |
| Statut | Select | Nouveau / Contacté / Répondu / Client / Pas intéressé |
| Ajouté à Lemlist | Checkbox | Tracking campagne |
| Notes perso | Rich text | Observations, angles d'approche |

## Skill — /prospect

**Fichier :** `.claude/skills/prospect/SKILL.md`

**3 flows :**
1. **LinkedIn Lead** — Coller un lien LinkedIn → Chrome MCP lit le profil → crée Lead + Agence dans Notion
2. **Website Audit** — Coller URL site agence → Chrome MCP charge la page → analyse UX/design → 3-5 problèmes + accroches email
3. **Screenshot Analysis** — Chemin fichier image → Claude Vision détecte les problèmes visuels

**Auto-détection :** le skill route automatiquement selon le type d'input (linkedin.com → Flow 1, http → Flow 2, fichier → Flow 3).

## Test réalisé

Lead test : **Romain Ragueneau** — Côté Particuliers Tours
- LinkedIn parsé via Chrome MCP
- Lead + Agence créés dans Notion avec relation bidirectionnelle
- Audit site : 8 problèmes détectés, score Moyen
- 3 accroches email personnalisées générées
- Portable direct trouvé sur le site et ajouté à la fiche

## Fichiers créés

```
.claude/skills/prospect/SKILL.md    — Skill definition
.env                                 — API keys (gitignored)
.gitignore                           — Protège .env et node_modules
docs/plans/2026-03-12-prospect-crm-design.md — Ce document
```

# PersonaLens — AI-Powered Lead Persona Engine

> Pipeline IA de génération automatique de personas B2B dans le CRM — n8n + GPT-4o

![Status](https://img.shields.io/badge/status-V1%20livré-brightgreen)
![Stack](https://img.shields.io/badge/stack-n8n%20%2B%20GPT--4o-blue)
![Portfolio](https://img.shields.io/badge/portfolio-PM%20IA-orange)

---

## Problème

Une équipe sales de 15 personnes perd **354 000 €/an** en qualification manuelle, personas obsolètes et désalignement sales/marketing. Créer une persona prend 30 minutes. Elle est souvent ignorée faute d'être dans le CRM au bon moment.

## Solution

Pipeline entièrement automatisé : de l'entrée d'un lead à la **persona card enrichie dans le CRM en moins de 15 secondes**.

```
Leads Input (Google Sheets)
→ Filter (nouveaux leads uniquement)
→ GPT-4o (génération persona JSON)
→ Code JS (parsing + validation qualité)
→ CRM Output (upsert par email)
→ Marquage traité
```

---

## Features V1

| Feature | Description |
|---|---|
| Persona enrichie | Profil, motivations, objections, déclencheurs achat, accroche idéale, arguments clés, erreurs à éviter, prochaine action |
| Score complétude | 0-100% sur 7 champs obligatoires |
| Détection email générique | gmail, yahoo, test.com... + alerte domaine incohérent |
| Statut intelligent | Fiable / À vérifier / Données incomplètes / À vérifier — email générique |
| Upsert natif | Zéro doublon CRM — mise à jour si lead existant |
| Traitement incrémental | Nouveaux leads uniquement via colonne `traite` |
| Batch processing | N leads traités en un seul run |

---

## Stack

| Brique | Prototype V1 | Production cible |
|---|---|---|
| Orchestration | n8n cloud | n8n self-hosted |
| IA génération | GPT-4o | GPT-4o + GPT-4o-mini |
| CRM | Google Sheets | HubSpot / Pipedrive |
| Enrichissement | Manuel | Clay + Apollo |
| Automation | n8n | n8n |

---

## Structure du repo

```
personalens-v1/
├── workflow.json          # Workflow n8n complet (import direct)
├── README.md              # Ce fichier
└── docs/
    ├── PRD_v2.1.docx      # Product Requirements Document
    └── CaseStudy_V1.docx  # Case study portfolio
```

---

## Installation

### Prérequis
- Compte [n8n.cloud](https://n8n.io) ou n8n self-hosted
- Clé API [OpenAI](https://platform.openai.com)
- Compte Google (pour Google Sheets)

### Import du workflow

1. Dans n8n, clique **+** → **Import from file**
2. Sélectionne `workflow.json`
3. Configure tes credentials :
   - **OpenAI** : Settings → Credentials → Add → OpenAI API
   - **Google Sheets** : Settings → Credentials → Add → Google Sheets OAuth2

### Configuration des sheets

Crée deux Google Sheets :

**Sheet 1 — Leads Input**
```
Prenom | Nom | email | titre | entreprise | secteur | taille_entreprise | pays | traite
```

**Sheet 2 — CRM Output**
```
lead_nom | lead_email | lead_titre | lead_entreprise | nom_persona | profil | categorie | score_maturite_achat | score_confiance | statut | date_generation | motivations | objections | style_communication | declencheurs_achat | accroche_ideale | arguments_cles | erreurs_a_eviter | prochaine_action | score_completude | alerte_email
```

### Lancer le pipeline

1. Ajoute des leads dans le **Sheet Leads Input** (colonne `traite` vide)
2. Dans n8n, clique **Execute Workflow**
3. Les personas apparaissent dans le **Sheet CRM Output**
4. Les leads traités sont automatiquement marqués `oui` dans la colonne `traite`

---

## Exemple de persona générée

```json
{
  "nom_persona": "RevOps Stratège",
  "profil": "Sophie Martin pilote les opérations de croissance chez Doctolib...",
  "categorie": "Champion Potentiel",
  "score_maturite_achat": 85,
  "score_confiance": 90,
  "statut": "Fiable",
  "score_completude": 100,
  "alerte_email": "✅ Email vérifié",
  "motivations": "Efficacité opérationnelle | Croissance durable | Adoption rapide",
  "objections": "Coût élevé | Complexité d'intégration",
  "declencheurs_achat": "Lancement produit | Partenariat stratégique",
  "accroche_ideale": "Optimisez vos opérations pour un revenu maximal.",
  "arguments_cles": "Solution éprouvée HealthTech | Compatibilité systèmes | ROI rapide",
  "erreurs_a_eviter": "Négliger l'intégration | Sous-estimer la formation",
  "prochaine_action": "Démo personnalisée + case studies secteur"
}
```

---

## Roadmap

- [x] **V1 — Lead Persona Engine** (ce repo)
- [ ] **V2 — Living Memory Engine** — enrichissement via transcriptions + RAG (n8n + pgvector)
- [ ] **V3 — Revenue Intelligence** — cartographie multi-stakeholders + signaux LinkedIn live

---

## Décisions techniques notables

**Prototype ≠ Production** — Google Sheets comme CRM pour valider la logique avant d'engager Clay + HubSpot. Même architecture, coût zéro, itération rapide.

**Upsert natif** — utilisation du node `Append or Update` n8n plutôt qu'un IF + Search complexe. Plus simple, plus robuste, maintenu par n8n.

**Traitement incrémental** — colonne `traite` dans le sheet d'entrée plutôt qu'une base de données. Suffisant pour le prototype, extensible vers Supabase en V2.

**Prompt engineering** — `response_format: json_object` + instructions explicites sur chaque champ pour éviter les hallucinations structurelles. Fallback `Array.isArray()` côté JS pour gérer les cas où GPT-4o renvoie une string au lieu d'un array.

---

## Documentation

- [PRD v2.1](docs/PRD_v2.1.docx) — Product Requirements Document complet (versions V1/V2/V3, KPIs, Risk Register)
- [Case Study](docs/CaseStudy_V1.docx) — Synthèse portfolio

---

*Portfolio PM IA & Automation — Mai 2026*

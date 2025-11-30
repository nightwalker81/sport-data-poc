# Kestra — Orchestration du Pipeline (POC Sport Data Solution)

Ce projet contient les **14 workflows Kestra** utilisés pour orchestrer l’ensemble du pipeline de traitement de données dans le cadre du **POC Sport Data Solution**.

L’orchestration est exécutée **localement via Docker Compose**.  
⚠️ Les credentials sont **enlevé volontairement** dans les fichiers de flow pour s'assurer la sécurité

## 📚 Table des matières

- [Structure du projet](#structure-du-projet)
- [Lancement de Kestra](#lancement-de-kestra)
- [Gestion des credentials (POC)](#gestion-des-credentials-poc)
- [Description des workflows](#description-des-workflows)
  - [Extraction](#extraction)
  - [Nettoyage](#nettoyage)
  - [Validation](#validation)
  - [Zone Curated](#zone-curated)
  - [Chargement](#chargement)
  - [Notification](#notification)
- [Ordre d'exécution du pipeline](#ordre-dexécution-du-pipeline)
- [Exécution manuelle d'un workflow](#exécution-manuelle-dun-workflow)
- [Résumé](#résumé)

---

## 📁 Structure du projet

```
kestra/
│── docker-compose.yml
│── README.md
└── flows/
    ├── clean_and_upload_activities.yml
    ├── clean_and_upload_rh.yml
    ├── clean_and_upload_sport.yml
    ├── curated_dim_employees.yml
    ├── curated_fact_activities.yml
    ├── curated_fact_avantages.yml
    ├── extract_activities_raw_from_s3.yml
    ├── extract_rh_raw_from_s3.yml
    ├── extract_sport_raw_from_s3.yml
    ├── load_curated_to_rds.yml
    ├── run_all_extracts.yml
    ├── send_slack_activity.yml
    ├── validate_activities_clean.yml
    └── validate_rh_clean.yml
```

---

## 🔧 Lancement de Kestra

Depuis le dossier `kestra/`, lancez l'orchestrateur localement :

```bash
docker compose up -d
```

Accédez à l’interface Kestra ici :  
👉 [http://localhost:8080](http://localhost:8080)

Pour arrêter l’environnement :

```bash
docker compose down
```

---

## 🔐 Gestion des credentials (POC)

Les identifiants sont **enlevés dans les fichiers de workflow** pour des raisons de sécurité :

- Credentials AWS (S3)
- Identifiants PostgreSQL (RDS)
- Webhook Slack
- Clé API Google Maps (si utilisée)

---

## 🔄 Description des Workflows

### 🟦 Extraction

| Workflow | Description |
|----------|-------------|
| `extract_activities_raw_from_s3` | Extraction des données brutes d’activités sportives depuis S3 |
| `extract_rh_raw_from_s3` | Extraction des données brutes RH depuis S3 |
| `extract_sport_raw_from_s3` | Extraction d’un second dataset sport depuis S3 |
| `run_all_extracts` | Exécute automatiquement les trois extractions ci-dessus |

### 🟩 Nettoyage

| Workflow | Description |
|----------|-------------|
| `clean_and_upload_activities` | Nettoyage du dataset "activities" et upload en version nettoyée |
| `clean_and_upload_rh` | Normalisation complète des données RH (format, noms, dates) |
| `clean_and_upload_sport` | Nettoyage du dataset brut "sport" |

### 🟨 Validation

| Workflow | Description |
|----------|-------------|
| `validate_activities_clean` | Contrôles qualité post-nettoyage : types, nulls, cohérences |
| `validate_rh_clean` | Contrôles qualité sur les données RH nettoyées |

### 🟧 Zone Curated

| Workflow | Description |
|----------|-------------|
| `curated_dim_employees` | Construction de la dimension "employés" à partir des RH enrichies |
| `curated_fact_activities` | Création de la table de faits des activités nettoyées et enrichies |
| `curated_fact_avantages` | Calcul des avantages :<br>• prime 5% si trajet sportif<br>• 5 jours bien-être si activité soutenue |

### 🟫 Chargement

| Workflow | Description |
|----------|-------------|
| `load_curated_to_rds` | Chargement des tables curated (DIM et FACT) dans PostgreSQL RDS |

### 🟪 Notification

| Workflow | Description |
|----------|-------------|
| `send_slack_activity` | Détection de la prochaine activité non envoyée + notification Slack + log dans PostgreSQL |

---

## 🔁 Ordre d'exécution du pipeline

Le pipeline suit une exécution **en cascade**, automatisée par les **triggers internes de Kestra** :

```
Extraction
   ↓
Nettoyage
   ↓
Validation
   ↓
Zone Curated
   ↓
Chargement (RDS)
   ↓
Notification Slack
```

---

## 🧪 Exécution manuelle d’un workflow

Dans l’interface graphique Kestra :

1. Accédez au menu **Flows**
2. Sélectionnez le workflow à exécuter
3. Cliquez sur **RUN**

> Tous les workflows sont indépendants et peuvent être lancés manuellement ou automatiquement via orchestration.

---

## 📘 Résumé

Ce projet contient :

- ✅ **14 workflows Kestra** prêts à l’emploi
- ✅ Une **orchestration complète** du pipeline de données
- ✅ L’intégration complète **S3 → Kestra → PostgreSQL (RDS) → Slack**
- ✅ Une stack exécutable en local via **Docker Compose**
- ✅ Des identifiants hardcodés (temporairement) pour usage en **POC privé**

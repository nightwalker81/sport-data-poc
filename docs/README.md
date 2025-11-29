# Documentation Technique — POC Sport Data Solution

Ce dossier contient l’ensemble des documents techniques nécessaires pour comprendre l’**architecture**, les **choix de conception** et la **structure des données** du POC **Sport Data Solution**.

Il constitue un **support de référence** pour :

- La soutenance orale
- Le rapport fonctionnel
- La présentation finale

---

## 🏗️ 1. Architecture du Pipeline de Données

Le dossier contient un schéma détaillé du pipeline, organisé en trois grandes étapes :

### 🔹 Ingestion

- **FastAPI** : Génération des activités sportives
- **GitHub** : Source des données RH
- **Airbyte** : Synchronisation vers S3
- **S3 (Zone RAW)** : Stockage des données brutes

### 🔹 Transformation (via Kestra Workflows)

- Extraction des données brutes depuis S3
- Nettoyage et validation
- Enrichissement (via API Google Maps si activée)
- Construction des tables **curated**
- Déclenchement de notifications **Slack**
- Orchestration complète grâce aux **triggers internes**

### 🔹 Chargement & Consommation

- Zone **Clean** & **Curated** sur S3
- Chargement des données enrichies dans **PostgreSQL (RDS)**
- Visualisation avec **Tableau**
- Monitoring & alerting (via logs Kestra et Slack)

📌 Ce schéma illustre la **chaîne de valeur complète** :  
**De la donnée brute aux insights business.**

---

## 🗄️ 2. Schéma Relationnel (Modèle de Données)

Le dossier inclut également le **modèle relationnel final**, utilisé pour l’analyse dans Tableau.

### 🟩 Tables créées dans PostgreSQL (RDS)

- `dim_employees`  
  → Données RH enrichies : activité physique, déplacements, jours bien-être, éligibilités…

- `fact_activities`  
  → Activités sportives **nettoyées**, **standardisées** et **enrichies**

- `fact_avantages`  
  → Résultat du calcul métier :
    - Montant de la prime
    - Nombre de jours bien-être
    - Coût total des avantages par employé

### 🔗 Relations principales

- `dim_employees.employee_id` 1️⃣ → 🔁 `fact_activities.employee_id`
- `dim_employees.employee_id` 1️⃣ → 🔁 `fact_avantages.employee_id`

📌 Ce schéma permet de comprendre le **modèle analytique** sur lequel repose le dashboard Tableau.

---

## 🖼️ 3. Contenu typique du dossier

```
docs/
│── architecture_pipeline.png         # Schéma d'architecture général
│── schema_relationnel.png            # Modèle relationnel (version image)
│── sports_events.drawio              # Fichier éditable du schéma
│── screenshots_kestra/               # Captures d’écran de l’UI Kestra
│── screenshots_tableau/              # Captures du dashboard Tableau
│── README.md                         # Ce fichier de documentation
```

---

## 🎯 4. Objectifs du dossier `docs/`

Le dossier `docs/` a été conçu pour :

- ✅ Appuyer la **présentation orale**
- ✅ Expliquer l’**organisation complète** du pipeline
- ✅ Documenter les **étapes de transformation**
- ✅ Justifier les **choix techniques** (Kestra, S3, RDS, Tableau, Slack)
- ✅ Servir de **référence durable** pour la relecture du POC

---

## 📝 5. Notes supplémentaires

- ⚠️ Les fichiers **`.drawio`** sont fournis pour permettre aux examinateurs ou membres de l’équipe de modifier les schémas si besoin.

- 🖼️ Les versions **PNG** sont prêtes à être intégrées dans des supports type **PowerPoint** ou documentation PDF.

---

📦 Ce dossier `docs/` complète le livrable technique et garantit une **compréhension globale** du système de bout en bout.


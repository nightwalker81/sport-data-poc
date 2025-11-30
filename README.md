# 🏅 Sport-Events — POC Pipeline de Données & Dashboard

Un projet complet d’**ingestion**, **transformation**, **orchestration**, **stockage**, **monitoring** et **visualisation** de données sportives.

---

## 📖 1. Contexte & Vision du POC

Le projet **Sport-Events** vise à encourager la pratique sportive chez les salariés en mettant en place un **système d’avantages automatisé**, basé sur les activités physiques.

### 🎯 Objectifs RH

- Encourager l’activité physique régulière
- Automatiser l’attribution de **primes** et **jours bien-être**
- Améliorer la culture d’entreprise et le bien-être global
- Assurer la **transparence via Slack**

### 🧪 Objectifs techniques du POC

- Construire un **pipeline de données complet & automatisé**
- Identifier et tester les **règles d’éligibilité**
- Calculer l’impact financier des avantages
- Créer un **dashboard décisionnel interactif**
- Prouver la **faisabilité technique** de bout en bout

> 📦 Ce repository contient **l’intégralité du POC**, de l’ingestion des données au tableau de bord final.

---

## 🏗️ 2. Architecture Générale du Système

L’architecture repose sur un pipeline **moderne** et **modulaire**, composé de plusieurs couches.

### 🔹 Sources

- `FastAPI` → Génération simulée d’activités sportives (type Strava)
- `GitHub` → Données RH versionnées
- `Airbyte` → Ingestion automatique vers S3 (zone RAW)

### 🔹 Transformation (via Kestra)

- Extraction des fichiers bruts depuis S3
- Nettoyage RH & sport
- Validation : types, nulls, cohérence métier
- Enrichissement : distance domicile/bureau, classification des sports
- Construction des tables **curated**
- Calcul des **avantages** (prime, jours bien-être)
- Notifications **Slack**

### 🔹 Stockage

- `AWS S3` : Zones **RAW → CLEAN → CURATED**
- `PostgreSQL (RDS)` : Tables analytiques **DIM/FACT** + logs

### 🔹 Visualisation

- `Tableau Desktop` : KPIs, courbes, graphiques, tables analytiques

### 🔹 Notification

- Webhook Slack : messages dynamiques après détection d’activités

> 📁 Des schémas détaillés sont disponibles dans le dossier [`/docs`](docs/).

---

## 🧩 3. Structure du Repository

```
sport-events-poc/
├── kestra/           # Workflows Kestra (14 fichiers) + docker-compose
├── tableau/          # Dashboard Tableau (.twbx)
├── docs/             # Architecture, schémas, captures d’écran
└── README.md         # Documentation globale du projet
```

---

## ⚙️ 4. Orchestration — Kestra (14 workflows)

L’orchestration via **Kestra** est le cœur du système. Les workflows sont organisés en 5 catégories.

### 🟦 A. Extraction (S3 RAW)

- `extract_rh_raw_from_s3`  
- `extract_sport_raw_from_s3`  
- `extract_activities_raw_from_s3`  
- `run_all_extracts` → lance les 3 précédents automatiquement

### 🟩 B. Nettoyage

- `clean_and_upload_rh`  
- `clean_and_upload_sport`  
- `clean_and_upload_activities`

### 🟨 C. Validation

- `validate_rh_clean`  
- `validate_activities_clean`

Contrôles appliqués :

- Types & formats
- Valeurs nulles
- Distances non négatives
- Cohérence des sports
- Duplications éventuelles

### 🟧 D. Zone Curated

- `curated_dim_employees`  
- `curated_fact_activities`  
- `curated_fact_avantages`

Enrichissements métiers :

- Distance domicile → bureau
- Validation des déplacements sportifs
- Classification des activités
- Calcul des **avantages** (prime, jours bien-être)

### 🟫 E. Chargement + Notification

- `load_curated_to_rds`  
- `send_slack_activity`

#### `send_slack_activity` :

- Détecte une nouvelle activité non encore notifiée
- Envoie un message Slack personnalisé
- Logue l’événement dans la table `slack_events_sent`

> 👉 Documentation complète dans [`/kestra/README.md`](kestra/README.md)

---

## 🗄️ 5. Base de Données PostgreSQL (RDS)

Le **modèle analytique** final contient :

### 🟩 Table de dimension

- `dim_employees`  
  → Données RH enrichies : primes, sports, distance, eligibility

### 🟦 Tables de faits

- `fact_activities` : Activités sportives nettoyées et enrichies  
- `fact_avantages` : Calcul complet des avantages

### 🟪 Table de log

- `slack_events_sent`  
  → Historique des activités déjà notifiées pour éviter les doublons Slack

#### 🔗 Relations clés

- `dim_employees.employee_id` ⟶ `fact_activities.employee_id`
- `dim_employees.employee_id` ⟶ `fact_avantages.employee_id`

📌 Schéma complet disponible dans [`/docs/schema_relationnel.png`](docs/sschema_relationel__sports_events.png)

---

## 📊 6. Dashboard Tableau

Le fichier `.twbx` contient le tableau de bord final avec :

### ⭐ KPIs

- Nombre total d’activités
- Salariés éligibles à la prime
- Salariés éligibles aux jours bien-être
- Coût total des avantages

### ⭐ Visualisations

- Courbe des activités par mois
- Bar chart des sports pratiqués
- Tableau récapitulatif RH (primes, jours, coûts)

📁 Fichier à ouvrir :  
`/tableau/sports_events_dashboard.twbx`  
📄 Documentation associée : [`/tableau/README.md`](tableau/README.md)

---

## 🔔 7. Notifications Slack

Lorsqu’une nouvelle activité est détectée :

- Un message motivant est envoyé sur Slack 🎉  
  (Nom, Prénom, sport, durée, message personnalisé)
- L’événement est logué dans la base PostgreSQL

### Objectifs :

- Créer une dynamique positive en interne
- Mettre en avant les bons comportements
- Favoriser un esprit communautaire autour du sport

---

## 🔐 8. Gestion des Credentials (POC)

Dans ce **POC strictement local et privé**, les credentials sont **enlevés** pour s'assurer la sécutité :

- AWS S3
- PostgreSQL RDS
- Slack Webhook
- Google API

---

## 🚀 9. Comment exécuter le POC

1️⃣ **Lancer Kestra** :

```bash
docker compose up -d
```

Accès interface : [http://localhost:8080](http://localhost:8080)

2️⃣ **Exécuter les workflows** :

- Soit lancer `run_all_extracts`
- Soit laisser les **triggers orchestrer automatiquement**

3️⃣ **Vérifier le chargement** :

- Le flow `load_curated_to_rds` doit être en `SUCCESS`

4️⃣ **Ouvrir Tableau** :

- Fichier : `sports_events_dashboard.twbx`

5️⃣ **Vérifier Slack** :

- Une notification doit apparaître pour chaque activité nouvelle

---

## 🏁 10. Conclusion

Ce POC démontre la capacité de **Sport-Events** à :

- Construire un **pipeline automatisé** de bout en bout
- Gérer et valoriser les **données sportives & RH**
- Mettre en place un **système d’avantages dynamique**
- Offrir un **dashboard stratégique** aux RH
- Déployer une **architecture cloud évolutive**

🚀 **Perspectives :**

- Industrialisation (CI/CD, sécurité, scalabilité)
- Connexion avec une API réelle (ex : FAST API)
- Analyse avancée (ML, scoring, segmentation)

---

📂 Tous les composants sont documentés dans leurs dossiers respectifs :  
[`/docs`](docs/) – [`/kestra`](kestra/) – [`/tableau`](tableau/)


# Tableau — Dashboard Sport Data Solution (POC)

Ce dossier contient le **dashboard Tableau** utilisé pour visualiser les résultats du **POC Sport Data Solution**.  
Il repose sur les données traitées et enrichies par le pipeline Kestra, stockées dans **PostgreSQL (RDS AWS)**, et fournit une visualisation directe des **KPIs métiers** et **avantages salariés**.

---

## 🧩 1. Connexion aux données

Le dashboard se connecte directement à la base de données suivante :

- 🟦 **PostgreSQL (AWS RDS)**

### Tables utilisées :

- `sports_events.dim_employees`
- `sports_events.fact_activities`
- `sports_events.fact_avantages`

📌 **La connexion est intégrée** dans le fichier `.twbx`.  
✅ **Aucun paramétrage n’est nécessaire**.

---

## 📊 2. KPIs affichés sur le tableau de bord

| KPI | Description |
|-----|-------------|
| ✔ **KPI 1 — Nombre total d’activités** | Nombre total d’activités sportives enregistrées sur l’année |
| ✔ **KPI 2 — Salariés éligibles à la prime** | Salariés venus au bureau en mode "sportif" (prime 5% brut) |
| ✔ **KPI 3 — Salariés éligibles bien-être** | Salariés ayant une activité soutenue (5 jours bien-être / an) |
| ✔ **KPI 4 — Coût total des avantages** | Montant total : primes + valeur des jours bien-être |

---

## 📈 3. Graphiques disponibles

### 📉 **Activités par mois** (courbe)
- Basé sur : `fact_activities.date_activities`
- Visualise la **saisonnalité des pratiques sportives**

### 📊 **Répartition des sports** (bar chart)
- Classement par fréquence
- Permet de voir les sports les plus populaires (ex : randonnée, running)

### 📋 **Tableau récapitulatif des salariés**
Données issues de `fact_avantages`. Ce tableau inclut :

- Nom / Prénom
- Montant jours bien-être
- Montant prime
- Nombre d'activités
- Éligibilité prime (True/False)
- Éligibilité bien-être (True/False)
- Coût total des avantages

---

## 🖥 4. Comment ouvrir le fichier `.twbx`

1. Installer **Tableau Desktop** (ou utiliser la version d’essai)
2. Télécharger le fichier `.twbx` depuis ce dossier
3. Double-cliquer pour l’ouvrir

📌 Toutes les **sources, connexions et structures** sont intégrées.  
Aucune configuration supplémentaire n’est requise.

---

## 📦 5. Préparation des données (provenance)

Les données affichées proviennent du pipeline de traitement suivant :

```
S3 RAW
  → Kestra (clean, validation, enrichissement)
    → S3 CURATED
      → PostgreSQL RDS
        → Tableau Desktop
```

> 🔍 Le dashboard utilise **exclusivement** les **tables curated** dans PostgreSQL.

---

## 🧪 6. Tests / Mise à jour du dashboard

Pour rafraîchir les données du tableau de bord :

1. Exécuter le pipeline Kestra (ou juste les flows nécessaires)
2. Vérifier que le flow `load_curated_to_rds` est terminé avec succès
3. Dans Tableau : `Data → Refresh All Extracts` (si applicable)

---

## 🏁 7. Résumé

Le dossier **Tableau** contient :

- ✅ Le **dashboard final** utilisé pour la soutenance
- ✅ L’ensemble des **KPIs** et **visualisations**
- ✅ Une version autonome `.twbx` **prête à l’emploi**
- ✅ Une connexion directe à la **base analytique du POC**

🎯 Ce dashboard illustre la **valeur métier du pipeline** :  
Transformez des **données brutes** en **insights actionnables**.



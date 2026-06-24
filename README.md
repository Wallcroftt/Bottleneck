---

# 🍷 Pipeline ETL & Qualité de Données - Bottleneck

## 📝 Contexte du Projet

Bottleneck est un marchand de vins prestigieux qui cherche à consolider ses données issues de deux systèmes distincts : un outil de gestion interne (ERP) et une plateforme de vente en ligne (Web).

L'objectif de ce projet est de construire un pipeline de données automatisé (ETL) capable de fusionner ces sources, de calculer le chiffre d'affaires global et de détecter les valeurs aberrantes (vins très haut de gamme), le tout en intégrant des **tests de qualité stricts** à chaque étape.

## 🛠️ Technologies Utilisées

* **Orchestration :** Kestra
* **Traitement de données :** Python (Pandas), SQL (DuckDB)
* **Conteneurisation :** Docker (pour l'isolation des environnements Python)

## ⚙️ Architecture du Pipeline

Le workflow automatisé (`bottleneck_quality_pipeline`) s'exécute mensuellement et réalise les opérations suivantes :

1. **Ingestion & Conversion :** Récupération de trois fichiers Excel bruts (ERP, Web, table de liaison) et conversion au format CSV. Renommage des clés de jointure pour assurer la cohérence.
2. **Nettoyage & Tests de Volume (DuckDB & Python) :** * Suppression des doublons et des valeurs nulles.
* Vérification stricte des volumétries attendues (825 références ERP, 714 références Web). Le pipeline s'interrompt (Fail) si les données sont incomplètes.


3. **Consolidation SQL :** Jointure des tables nettoyées pour calculer le Chiffre d'Affaires par produit (`price * total_sales`).
4. **Calculs Statistiques & Tests Financiers :**
* Vérification du volume post-jointure (714 lignes attendues).
* Calcul du **Z-Score** pour isoler les prix atypiques (détection de 30 vins premium).
* Validation du chiffre d'affaires total (70 568.60 €).


5. **Génération des Livrables :** Création automatique d'un rapport financier complet et séparation des catalogues ("vins premium" vs "vins ordinaires").

## 🚀 Comment déployer ce projet

### Prérequis

* Avoir une instance [Kestra](https://kestra.io/) fonctionnelle (en local via Docker ou sur le cloud).
* Disposer des fichiers sources (`erp.xlsx`, `web.xlsx`, `liaison.xlsx`).

### Installation

1. Copier le contenu du fichier `flow.yaml` présent dans ce dépôt.
2. Dans l'interface utilisateur de Kestra, naviguer vers l'onglet **Flows** et créer un nouveau flux.
3. Coller le code YAML et sauvegarder.
4. Lancer une exécution manuelle (Bouton *Execute*) en chargeant les trois fichiers Excel dans les variables d'entrée (Inputs) prévues à cet effet.

---
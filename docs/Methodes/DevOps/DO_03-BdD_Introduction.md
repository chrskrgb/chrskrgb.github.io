---
title: Introduction aux Bases de Données
tags:
  - Document
  - DevOps
  - IT
  - BDD
---

# Introduction aux Bases de Données

## Typologie et Classification des Données

### Concept et utilité
- **Définition** : Catégorisation des flux d'information déterminant l'architecture de stockage cible.
- **Rôle et avantages** :
	- Choix technologique optimal selon la nature et la variabilité des données d'entreprise.
	- Premier critère décisionnel pour arbitrer entre un système relationnel et un système non relationnel.

### Catégories de données
1. **Données structurées**
	- Format tabulaire standardisé défini par des schémas stricts (lignes et colonnes).
	- Cible privilégiée : Systèmes de gestion de bases de données relationnelles (**SGBDR**).
	
2. **Données semi-structurées**
	- Données contenant des marqueurs organisationnels sans schéma rigide prédéfini.
	- Formats types : `XML`, `JSON`, `CSV`.
	
3. **Données non structurées**
	- Données brutes dépourvues de structure conceptuelle formelle.
	- Formats types : Fichiers texte, audio, vidéo, photographies.
	- Cible privilégiée : Écosystèmes **NoSQL** ou **Data Lakes**.

---

## Bases de Données, fondamentaux 

### Concept et utilité
- **Définition** : Système logiciel dédié au stockage persistant, à l'organisation et à la restitution de l'information.
- **Rôle et avantages** :
	- **Stockage et extraction** : Organisation optimisée et requêtage rapide des enregistrements.
	- **Partage concurrentiel** : Accès simultané multi-utilisateurs maîtrisé.
	- **Intégrité des données** : Maintien de la cohérence via des contraintes relationnelles (clés étrangères).
	- **Sécurité** : Contrôle granulaire des accès, confidentialité et conformité d'audit.
	- **Évolutivité** : Capacité d'extension technique face à l'accroissement des volumes traités.

---
## Typologie des Bases de Données

### Bases de données relationnelles (SGBDR)
- Modèle de stockage :
	- Organisation tabulaire stricte stockée physiquement par lignes.
	- Langage standard d'interrogation : `SQL`.
	- **Restriction** : Schéma statique rigide imposé dès la conception.
- Solutions :
	- **MySQL**, **PostgreSQL**, **Oracle Database**, **SQLite**, **Microsoft SQL Server**.

### Bases de données NoSQL
- Caractéristiques :
	- Conçues pour le traitement massif de données (**Big Data**).
	- Haute disponibilité, tolérance au partitionnement et scalabilité horizontale native.
- Sous-familles spécialisées :
	1. **Orientées documents**
		- Stockage sous le paradigme clé-valeur où la valeur est un document semi-structuré (`JSON`/`BSON`).
		- **Avantage principal** : Schéma dynamique très flexible évitant les jointures relationnelles coûteuses.
		- **Astuce** : Requêtage de données hiérarchiques complexes via une clé unique.
		- **Restriction** : Inadaptées aux données purement non structurées ; risque d'erreurs en l'absence de validation de structure.
		- Cas d'usage : Catalogues de produits, fiches clients unifiées, analyses de pages web.
		- Moteurs types : **MongoDB**, **ElasticSearch**, **Couchbase**.
		
	2. **Orientées graphes**
		- Modèle mathématique reposant sur des nœuds, des propriétés et des relations typées.
		- **Avantage principal** : Performances extrêmes sur les données interconnectées et application native d'algorithmes de théorie des graphes.
		- **Restriction** : Inefficaces hors des contextes de forte interconnexion relationnelle.
		- Cas d'usage : Réseaux sociaux, moteurs de recommandation, détection de fraude organisée.
		- Moteurs types : **Neo4j**, **OrientDB**, **ArangoDB**.
		
	3. **Orientées colonnes**
		- Stockage physique regroupé par colonnes au lieu de lignes traditionnelles.
		- **Avantage principal** : Colonnes dynamiques libérant l'espace disque (non-stockage des valeurs `NULL`) et accélérant les agrégations massives.
		- **Restriction** : Non adaptées aux flux de données complètement non structurés.
		- Cas d'usage : Traitement d'événements à haute fréquence, télémétrie IoT, capteurs temps réel, suivi logistique.
		- Moteurs types : **Apache Cassandra**, **Apache HBase**.

---
## Méthodologie d'Arbitrage et Déploiement

### Démarche de sélection d'une base de données
1. **Évaluation des besoins applicatifs**
	- Qualification de la nature des flux : Structurés, semi-structurés ou non structurés.
	- Analyse du mode d'ingestion : Traitement temps réel ou traitement par lots (**Batch**).
	
2. **Dimensionnement technique et scalabilité**
	- Anticipation des volumes totaux et des pics de charge réseau.
	- Choix du mode d'extension : Scalabilité verticale (accroissement matériel) ou horizontale (ajout de nœuds).
	
3. **Audit de performance et sécurité**
	- Intégration de mécanismes accélérateurs : Indexation, mise en cache (**Caching**), compression.
	- Mesures de protection : Chiffrement, traçabilité des accès et audit de conformité.
	
4. **Viabilité financière et pérennité**
	- Calcul des coûts initiaux d'infrastructure et de maintenance opérationnelle récurrente.
	- Vérification de l'écosystème : Richesse de la communauté, support des connecteurs et outillage tiers.
	- **Règle absolue** : La coexistence de plusieurs technologies complémentaires (polyglot persistence) est fréquente pour couvrir des besoins distincts au sein d'un même projet.

### Hébergement Cloud
- Infrastructures infogérées pour le déploiement de bases de données managées : **AWS**, **Microsoft Azure**, **Google Cloud Platform** (`GCP`), **OVHcloud**.

---
## Architectures Analytiques : Data Warehouse et Data Lake

### Data Warehouse
- **Concept et utilité** :
	- Entrepôt centralisé organisant les données consolidées de l'entreprise à des fins d'analyse décisionnelle et de reporting.
	- Alimentation assurée par des chaînes d'intégration orchestrées (**ETL** / **ELT**).
	- Distribution aval via des sous-ensembles thématiques spécialisés (**Data Marts**) alimentant la **BI**, le reporting et la visualisation de données.
	
- Caractéristiques fonctionnelles fondamentales :
	- **Orienté sujet** : Structuration alignée directement sur les métiers de l'organisation.
	- **Variable dans le temps** : Historisation continue assurant l'analyse de tendances temporelles.
	- **Non volatile** : Données figées accessibles exclusivement en lecture, sans modification destructrice.
	- **Intégré** : Normalisation unifiée de sources hétérogènes éliminant les silos internes.

### Data Lake
- **Concept et utilité** :
	- Dépôt central conservant les données brutes sous leurs formats natifs sans transformation préalable.
	- Supporte simultanément les données structurées, semi-structurées et non structurées (fichiers bruts).
	- Débouchés techniques : Alimentation de Data Warehouses nettoyés et entraînement de modèles de **Machine Learning**.
	
- Modèles d'organisation des fichiers :
	- **Hiérarchique** : Structure classique arborescente (dossiers et sous-dossiers).
	- **Plat (Flat storage)** : Organisation décloisonnée s'appuyant sur des métadonnées et un système d'étiquetage par tags.
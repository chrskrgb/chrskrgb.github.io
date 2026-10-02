---
title: Introduction aux Concepts DevOps
tags:
  - Document
  - DevOps
  - IT
  - Méthode
---

# Introduction aux Concepts DevOps

## Concepts DevOps

### Concept et utilité
- **Définition** : Pratiques / outils, automatisant / intégrant les flux entre les équipes de dev et des ops.
- **Rôle et avantages** : 
	- Raccourcir le cycle de vie du développement logiciel (Time-to-Market).
	- Supprimer les frictions d'intégration et de déploiement (via une vision commune).
	- Instauration d'un retour d'information continu via des tests.
	- **Règle** : Le **DevOps** n'est ni un outil unique ni un langage, mais une démarche méthodologique.

### Agilité et Principes clés
- Complémentarité :
	- **Agile** réconcilie le développement avec les clients.
	- **DevOps** réconcilie le développement avec les opérations.
	
- **Piliers fondamentaux** :
	- **Développement itératif et incrémental** : Livrer d'abord une version fonctionnelle, puis l'améliorer.
	- **Volatilité des exigences** : Intégrer et exploiter le changement en continu.
	- **Boucles de feedback courtes** : Renforcer la communication bidirectionnelle continue.

### Cycle de vie et Pratiques (CI/CD)
1. Phase DEV - Intégration Continue (**CI**)
	- **Planifier** : Pratiques agiles alignées sur les besoins métier.
	- **Coder** : Conception logicielle collaborative sur un gestionnaire de code source unique.
	- **Tester** : Tests continus et automatisés pour valider la non-régression.
	- **Compiler** : Construction des livrables exploitables.
	
2. Phase OPS - Déploiement et Exploitation (**CD**)
	- **Livraison Continue** (`CD`) : Automatisation jusqu'aux tests de pré-production.
		- **Impératif** : Validation manuelle requise pour la bascule finale en production.
	- **Déploiement Continu** (`CD`) : Déploiement entièrement automatisé en production sans intervention humaine.
	- **Exploitation** : Maintien en conditions opérationnelles des services.
	- **Supervision** : Monitoring et détection continue des incidents en production.

### Bonnes pratiques d'intégration
- Centralisation : Unicité stricte du code source sur un dépôt **Git**.
- Suivi : Traçabilité des versions et revues de code systématiques.
- Automatisation : Déclenchement automatique des suites de tests à chaque modification.
- Modularité : Encapsulation des composants applicatifs derrière des interfaces standardisées (**APIs**).

---
## Architecture des APIs

### Rôle et avantages
- **Définition** : Interface de communication normalisée reliant un client (consommateur) à un service applicatif ou de données.
- **Avantage** : Découplage de la logique métier sous-jacente par rapport aux utilisateurs finaux.

### Analogie fonctionnelle et Outils
1. Composants d'une interface API
	- **Client** : Utilisateur ou programme émetteur de la requête.
	- **Menu** : Documentation spécifiant les requêtes autorisées et attendues.
	- **Serveur (API)** : Intermédiaire validant la conformité de la requête et routant les données.
	- **Cuisine (Service)** : Backend traitant le calcul ou extrayant la donnée demandée.
2. Écosystème de développement
	- Frameworks Web : `Flask`, `FastAPI`, `Django`.
	- Cas d'usage : Services météo (**OpenWeatherMap**), manipulation de données (**Pandas**), passerelles tierces (**Google Translate**).

---
## Tests Unitaires

### Concept et utilité
- **Définition** : Méthode de validation logicielle isolant chaque brique unitaire du code de manière autonome.
- **Rôle et avantages** :
	- Détection immédiate des régressions dès la modification du code.
	- **Impératif** : Doivent être automatisés au sein des pipelines de CI.

---
## Virtualisation et Conteneurisation

### Concept et utilité
- **Définition** : Environnements d'exécution isolés garantissant la reproductibilité d'un livrable informatique.
- **Rôle et avantages** :
	- Élimination du problème de cohérence logicielle (« ça marche sur ma machine »).
	- Isolation stricte des dépendances et élimination des conflits de versions entre services.

### Approches techniques
1. Environnements Virtuels
	- Encapsulation des librairies requises localement par projet.
	- **Restriction** : Restent dépendants du système hôte sous-jacent.
2. Conteneurs applicatifs
	- Regroupement hermétique du code, des exécutables, des dépendances et de la configuration.
	- **Règle absolue** : Portabilité totale dès lors que l'infrastructure cible dispose d'un **Container Runtime** valide (ex. **Docker**).

---
## Gestion de Version et CI/CD (Git & GitHub)

### Composants fondamentaux
1. **Git**
	- Gestionnaire de versions distribué.
	- **Rôle** : Historique des modifications et organisation du travail sur des branches parallèles (`main`/`master`, `develop`/`feature`).
	
2. **GitHub**
	- Plateforme d'hébergement distant et de travail collaboratif.
	- Rôle : Centralisation des dépôts distants et orchestration d'intégrations tierces.

### Automatisation avec GitHub Actions
1. **Events** : Déclencheurs de flux (ex. `push`, `pull_request`, exécution planifiée `schedule`).
2. **Workflows** : Fichiers déclaratifs définissant les pipelines automatisés.
3. **Jobs** : Tâches d'exécution regroupées tournant sur des **Runners** dédiés.
4. **Steps** : Séquences d'instructions unitaires et de commandes exécutées au sein de chaque job.
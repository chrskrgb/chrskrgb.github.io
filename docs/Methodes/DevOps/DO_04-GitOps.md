---
title: GitOps & Infrastructure as Code
tags:
  - Document
  - DevOps
  - IT
  - Méthode
  - Git
---

# GitOps & Infrastructure as Code

## Fondamentaux du GitOps

### Concept et utilité
- **Définition** : Cadre opérationnel exploitant les dépôts **Git** comme unique source de vérité pour orchestrer et gérer l'infrastructure via des fichiers déclaratifs.
- **Équation fondamentale** : $GitOps == IAC + MRs + CICD$.
- **Rôle et avantages** :
	- Rapprochement des pratiques de gestion d'infrastructure avec les meilleures pratiques de développement applicatif.
	- Automatisation complète des déploiements et élimination des configurations manuelles sur les serveurs.
	- **Règle** : Toute modification de l'état souhaité (**desired state**) doit obligatoirement transiter par un commit ou une demande de fusion (**Merge Request**).

### Piliers
1. **IaC - Infrastructure as Code**
	- Description déclarative complète de l'état attendu de l'infrastructure.
	- Centralisation et versionnement strict des configurations dans un dépôt distant **Git**.
	
2. Merge Requests (**MRs** / Pull Requests)
	- Mécanisme unique de validation et de promotion de tout changement opérationnel.
	- Garantie d'une revue de code collaborative rigoureuse avant fusion sur la branche principale (`main` ou `master`).
	
3. Intégration / Déploiement Continus (**CI/CD**)
	- Exécution automatisée des builds, analyses statiques et tests unitaires à chaque modification.
	- **Boucle de réconciliation** : Alignement automatique de l'état réel de l'infrastructure sur l'état cible déclaré dans **Git** dès la détection d'une dérive (**drift**).

---
## Modèles d'Exécution et Flux GitOps

### Comparatif des modèles d'architecture
1. Modèle Push (**Push Model**)
	- Un agent externe (pipeline de **CD**) détient les accès directs à l'infrastructure cible et pousse les modifications.
	- Déclenchement consécutif à la validation d'un commit ou au succès d'un pipeline de **CI**.
	- **Restriction** : Nécessite d'exposer les identifiants et certificats d'accès du cluster au système externe de CI/CD.
	
2. Modèle Pull (**Pull Model**)
	- Un agent opérateur s'exécute à l'intérieur même du cluster (ex. cluster **Kubernetes**).
	- Scrutation active et périodique des dépôts **Git** et des registres d'images conteneurs.
	- **Avantage principal** : Mise à jour de l'environnement orchestrée de l'intérieur, renforçant la sécurité du cluster.

### Cycle de promotion d'une version (Full Release Flow)
1. Qualification initiale (Branche de développement)
	- Validation du commit applicatif et déclenchement de la **CI** (tests, analyse de code).
	- Construction et publication de l'image **Docker** vers le registre dédié (ex. **Artifactory**).
	- Mise à jour automatique des manifestes de test dans le dépôt de configuration.
	
2. Synchronisation en environnement de test
	- L'opérateur **GitOps** synchronise le cluster cible sur les manifestes d'intégration.
	- Exécution des validations automatisées (tests d'intégration et de sécurité).
	
3. Promotion en environnement de Staging
	- Création d'une **Merge Request** validant la mise à jour des manifestes de staging.
	- Déploiement automatique dès l'approbation de la requête.
	- Campagnes d'audit : Tests de performance, tests d'intrusion (**PenTest**) et validations manuelles du propriétaire.
	
4. Promotion finale en Production
	- **Impératif** : Émission et approbation formelle d'une **MR** de mise à jour des manifestes de production.
	- Synchronisation finale exécutée par l'outil **GitOps** vers le cluster de production.

---
## Bénéfices Stratégiques et Comparatif Opérationnel

### Gains apportés par GitOps
- **Automatisation & Vélocité** : Déploiements plus fréquents, rapides et reproductibles sans actions manuelles.
- **Cohérence & Documentation** : Systèmes entièrement auto-documentés via l'historique **Git** facilitant la duplication d'environnements.
- **Résilience opérationnelle** : Réduction drastique du temps moyen de reprise d'activité (**MTTR**) grâce aux retours arrière immédiats (`rollback` / `roll forward`).
- **Sécurité & Conformité** : Gestion granulaire des accès, traçabilité exhaustive et contrôle strict des dérives de configuration.

### Rapprochement : Démarche traditionnelle vs GitOps
1. Démarche manuelle ou automatisée classique
	- Modifications ponctuelles non tracées via liaisons distantes directes (`SSH`, `FTP`).
	- Risque d'absence d'historique centralisé et audits de conformité partiels.
	
2. Cadre opérationnel GitOps
	- Immuabilité des déploiements : Aucun accès direct d'écriture hors synchronisation déclarative.
	- Processus d'approbation et de validation systématiquement intégré aux flux de fusion.

---
## Outillage et Implémentation avec Flux CD

### Écosystème logiciel
- Solutions majeures du marché : **Flux CD**, **Argo CD**, **Atlantis**, **PipeCD**, **Fleet**.

### Déploiement et Configuration de Flux CD
1. Prérequis d'infrastructure
	- Cluster **Kubernetes** opérationnel : Solutions locales (**Minikube**, **Docker Desktop**, **Kind**, **Rancher Desktop**) ou cluster complet hébergé.
	
2. Topologies d'installation
	- **Non Haute Disponibilité (Non-HA)** : Réservée aux phases d'évaluation, tests et prototypage local.
	- **Haute Disponibilité (HA)** :
		- **Impératif** : Configuration recommandée pour les environnements de production.
		- **Restriction** : Nécessite impérativement au minimum 3 nœuds de travail (**worker nodes**).
		
	- **Installation légère (Core)** : Version sans interface utilisateur (`UI`) ni serveur API externe, dédiée aux tâches d'administration pure.
	
3. Gestion des privilèges d'accès
	- `Cluster-admin` : Droits d'administration totaux pour déployer sur l'ensemble du cluster hôte.
	- `Namespace` : Isolement des droits restreignant les déploiements hors du périmètre de l'opérateur.
	- `Custom` : Définition granulaire de droits personnalisés d'édition ou de consultation (`Edit` / `View`).
	
4. Formats de distribution des manifestes
	- Déploiement supporté via fichiers bruts `YAML`, packages `Helm` ou personnalisations `Kustomize`.
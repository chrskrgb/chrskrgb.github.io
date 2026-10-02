---
title: Déploiement d'Applications Cloud & CI/CD
tags:
  - Document
  - DevOps
  - IT
  - Méthode
  - CI/CD
  - Cloud
---

# Déploiement d'Applications Cloud & CI/CD

## Origines et Principes Fondamentaux de DevOps

### Concept et utilité
- **Définition** : Rapprochement structurel entre les équipes de développement (projets/évolutions) et les opérations (run/exploitation) pour appréhender l'ensemble de la chaîne de valeur logicielle.
- **Origines méthodologiques** :
	- Évolution des approches agiles combinée aux principes de la production allégée (**Lean Manufacturing** / **Lean Software Development**).
	- Révolution technologique rendue possible par la **virtualisation**, le **cloud computing** et l'**Infrastructure as Code**.
	
- **Rôle et avantages** :
	- Réduire le délai de mise sur le marché (**Time-to-Market**) en accélérant la boucle de rétroaction utilisateur.
	- Réduire la **dette technique** et éliminer la fragilité structurelle des systèmes.
	- Tester rapidement des hypothèses produit et valider des opportunités business.

### Élimination des gaspillages (Lean Waste)
- Les sept formes de déchets éliminées par la démarche :
	1. **Anomalies et bogues** (`Defects`)
	2. **Changements de contexte** (`Task switching` / commutation de tâches)
	3. **Fonctionnalités superflues** (`Extra features` / surproduction)
	4. **Passages de relais** (`Handoffs` / transferts de responsabilités)
	5. **Délais d'attente** (`Delays`)
	6. **Travail partiellement achevé** (`Partially done work` / encours non finalisé)
	7. **Réapprentissage et reprises** (`Relearning` / traitements superflus)

### Gestion de la dette technique
- Définition du risque : Mauvaises décisions d'ingénierie et compromis pris pour accélérer le développement immédiat au détriment de l'architecture.
- Conséquences directes : Allongement continu des temps de développement futurs et multiplication exponentielle des régressions.
- **Règle absolue** : Coder au plus vite sans rigueur contracte une dette dont les intérêts se payent tout au long du cycle de vie du produit.

---
## Cartographie de la Chaîne de Valeur et Indicateurs de Flux

### Indicateurs clés (Value Stream Mapping)
1. **Lead Time (LT)**
	- Temps total écoulé entre la réception d'une demande et sa mise à disposition effective en aval.
	- Englobe le temps de traitement actif ainsi que tous les temps morts d'attente.
	
2. **Process Time (PT)**
	- Temps effectif consacré à la réalisation active de la tâche (« touch time »).
	
3. **Percent Complete and Accurate (%C/A)**
	- Mesure de la qualité du travail transmis entre étapes consécutives.
	- **Règle absolue** : Seule l'étape en aval est habilitée à évaluer le `%C/A` de l'étape précédente.
	- Calcul : Pourcentage de livrables exploitables en l'état sans nécessiter de corrections, précisions ou ajouts.

### Optimisation des goulots d'étranglement
- Identification visuelle : Repérer précisément les étapes saturées et les stocks tampons qui ralentissent l'ensemble du flux.
- Application de la théorie des contraintes :
	- **Impératif** : Éviter les pièges de l'optimisation locale.
	- Prioriser les efforts d'automatisation sur le goulot d'étranglement principal causant le retard global maximal.
	
- Définition de terminé (**Definition of Done**) :
	- **Règle absolue** : Une tâche n'est terminée que **quand le client a reçu la valeur attendue** en environnement de production.
	- Le travail ne peut être considéré comme achevé si les étapes d'intégration, de test et de mise en exploitation réelle ne sont pas franchies.

---
## Architecture des Pipelines CI/CD

### Étapes structurelles du flux continu
1. Intégration Continue (**CI**)
	- Processus d'ingénierie automatisant la compilation, l'exécution des tests unitaires et le déploiement sur plateformes d'intégration.
	- Objectif : Valider systématiquement la non-régression et détecter les régressions au plus tôt.
	- **Astuce** : Les équipes de développement peuvent instancier les tests d'intégration en autonomie, sans intervention de l'exploitation.
	
2. Livraison Continue (**CD** - Continuous Delivery)
	- Automatisation du packaging et des tests fonctionnels, d'intégration et de validation jusqu'aux portes de la production.
	- **Restriction** : La bascule finale vers l'environnement de production requiert impérativement une validation ou approbation manuelle.
	
3. Déploiement Continu (**CD** - Continuous Deployment)
	- Déploiement automatique en production dès le passage avec succès de l'intégralité des suites de tests automatisés.
	- Contrôle post-déploiement : Observation immédiate des métriques de supervision et des impacts de performance.
	- **Impératif** : Présence obligatoire d'une procédure automatisée de retour arrière (`rollback`) en cas d'anomalie.

### Pipeline parallèle et optimisation
- Parallélisation : Exécution simultanée des étapes d'analyse pour raccourcir drastiquement les boucles de validation.
- Économie de calcul : Arrêt immédiat des flux dès la première défaillance pour éviter la consommation inutile de ressources.
- Traçabilité : Génération systématique de traces d'audit horodatées à chaque transition d'état.

---
## Gestion de Configuration et Contrôle de Versions

### Exhaustivité du Versioning
- **Règle absolue** : Le système de gestion de versions (**VCS**) doit centraliser **l'intégralité des composants** du système d'information, et non pas uniquement le code applicatif.
- Éléments sous contrôle de versions strict :
	- Code source applicatif et suites de tests automatisés.
	- Scripts de création, de migration et de manipulation des bases de données.
	- Scripts de build, scripts d'instanciation des environnements et de déploiement.
	- Fichiers de configuration, bibliothèques, dépendances, documentations et métadonnées d'artefacts.
	- Outils et environnements de développement (compilateurs, configurations d'IDE).

### Administration des environnements de production
- Immuabilité des infrastructures :
	- **Impératif** : Toute modification d'environnement s'effectue exclusivement par des scripts versionnés.
	- **Restriction** : Interdiction absolue pour les administrateurs et exploitants d'intervenir ou de modifier manuellement la production.
	
- Cycle de vie éphémère : Création dynamique des environnements lors de l'exécution du pipeline, puis destruction/libération automatique après exécution.
- Traitement immédiat des incidents :
	- **Règle absolue** : Tout défaut ou échec constaté sur un script de pipeline doit être corrigé sur-le-champ.
	- Rejouer les étapes problématiques aussi souvent que possible afin d'ajuster durablement la méthodologie de travail.

---
## Pratiques Opérationnelles et Culture d'Équipe

### Transformation des méthodes de travail
1. Lots de livraison réduits (**Small Batches**)
	- Scission des livrables massifs en unités réduites déployées fréquemment (quotidiennement ou hebdomadairement).
	- Bénéfice : Réduction de la variabilité opérationnelle et facilitation de l'apprentissage continu.
	
2. Distinction entre livraison et déploiement
	- **Déploiement** : Opération technique installant le logiciel sur les serveurs de production.
	- **Livraison** : Décision business activant l'accès à la fonctionnalité pour les utilisateurs cibles.
	
3. Gestion visuelle par flux tiré (**Kanban**)
	- Visualisation continue de l'état d'avancement et réduction drastique du besoin de réunions de coordination.
	- **Règle absolue** : Limitation stricte du travail en cours (**WIP**) pour fluidifier la traversée des tâches.
4. Allocation dédiée à l'innovation
	- Répartition continue du temps d'équipe (ex. 20% alloué) pour expérimenter de nouvelles solutions techniques et créer des outils internes.

### Compétences et profils d'équipe
- Équipes autonomes et multidisciplinaires favorisant les profils aux compétences élargies (**T-Shaped skills**).
- Métiers spécialisés :
	- **Administrateur outillage DevOps** : Responsable de la gouvernance des chaînes d'outils et des plateformes cloud.
	- **Architecte Cloud** : Expert de la conception d'infrastructures résilientes et scalables.
	- **Site Reliability Engineer (SRE)** : Ingénieur garant de la disponibilité, de la stabilité et des performances des architectures à haute charge.

---
## Écosystème Technique et Chaîne d'Outillage

### Outils de la chaîne DevOps
1. **Planification et collaboration**
	- Gestion de projets et documentation partagée : **Jira**, **Confluence**.
	
2. **Gestion de code source (SCM)**
	- Systèmes centralisés et distribués : **Git**, **GitHub**, **GitLab**, **Bitbucket**, **Subversion (SVN)**.
	
3. **Construction du code (Build)**
	- Outils d'automatisation de build et packaging : **Maven**, **Gradle**, **SBT**.
	
4. **Intégration Continue et Tests (CI & Test)**
	- Moteurs d'orchestration CI : **Jenkins**, **GitLab CI**, **CircleCI**, **Travis CI**, **TeamCity**, **Codeship**, **Bamboo**.
	- Frameworks de tests automatisés : **Selenium**, **JUnit 5**.
	
5. **Déploiement et Conteneurisation (Deploy)**
	- Runtimes et orchestrateurs : **Docker**, **Kubernetes**, **DC/OS**.
	
6. **Infrastructure as Code et Configuration**
	- Automatisation et gestion d'état : **Terraform**, **Ansible**, **Chef**, **SaltStack**.
	
7. **Exploitation, Supervision et Monitoring (Operate & Monitor)**
	- Tableaux de bord et métriques : **Datadog**, **Nagios**, **Splunk**, plateformes cloud comme **AWS**.
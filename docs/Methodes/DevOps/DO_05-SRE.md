---
tags:
  - Document
  - DevOps
  - IT
  - Méthode
  - SRE
---
## Fondamentaux du Site Reliability Engineering - SRE

### Concept et utilité
- **Définition** : Discipline appliquant les principes de l'ingénierie logicielle aux opérations informatiques afin d'automatiser la gestion des systèmes et la surveillance applicative.
- **Rôle et avantages** :
	- Maximisation de la disponibilité et de la fiabilité des services en production.
	- Réduction proactive de l'impact opérationnel des interruptions de service.
	- **Règle absolue** : La défaillance logicielle est inévitable ; la planification et l'automatisation de la réponse aux incidents sont obligatoires.

### Comparatif SRE vs DevOps
1. Points de convergence
	- Objectif commun de décloisonnement entre équipes de développement et des opérations.
	- Partage de valeurs clés : automatisation poussée, supervision continue et responsabilité collective.
	
2. Distinctions fondamentales
	- **DevOps** : Philosophie et transformation culturelle axée sur l'accélération continue de la livraison de valeur.
	- **SRE** : Implémentation pragmatique et prescriptive menée par les équipes d'ingénierie logicielle pour assurer la résilience à grande échelle.

---
## Piliers de la Fiabilité et Métriques de Service

### Piliers de la fiabilité système
1. **Disponibilité**
	- Mesure de l'accessibilité du service face aux requêtes des utilisateurs.
	- Leviers d'optimisation : mise en cache distribuée, réduction de la latence réseau et optimisation fine des requêtes.
	
2. **Latence**
	- Temps de réponse mesuré pour le traitement d'une requête unitaire.
	- **Impératif** : Maintien d'un seuil minimal de latence garantissant une expérience utilisateur satisfaisante.
	
3. **Débit**
	- Volume de requêtes traitées par unité de temps.
	- Leviers d'optimisation : algorithmes à haute efficacité, caches applicatifs et bandes passantes étendues.
	
4. **Capacité**
	- Volume de ressources matérielles et logicielles allouées pour absorber la charge.
	- Leviers d'optimisation : mécanismes d'auto-scaling, optimisation des allocations et planification capacitaire.

### Métriques contractuelles (SLA, SLO, SLI)
- Définition transverse : Indicateurs quantifiables de la qualité de service applicative.
1. **SLA (Service Level Agreement)**
	- Engagement contractuel liant formellement le fournisseur de service au client final.
	- Bonnes pratiques : rédaction en langage clair, alignement sur les attentes métier et implication directe des équipes d'ingénierie.
	
2. **SLO (Service Level Objective)**
	- Objectif interne chiffré fixé par l'équipe **SRE** pour piloter le niveau de fiabilité attendu.
	- **Règle absolue** : Les SLO doivent être mesurables, atteignables et limités en nombre pour cibler l'essentiel.
	
3. **SLI (Service Level Indicator)**
	- Mesure factuelle en temps réel de la conformité du service par rapport au SLO défini.

---
## Bonnes Pratiques d'Exploitation et Gestion des Incidents

### Exploitation et déploiement
1. Revue de code
	- Contrôle systématique du code source validant le respect strict des critères de performance, sécurité et fiabilité.
	- Détection amont des anomalies avant tout déploiement en production.
	
2. Automatisation des tests
	- Exécution automatique des validations : tests fonctionnels, de non-régression, de performance et d'audit de sécurité.
	
3. Supervision et observabilité
	- Surveillance continue pour une identification immédiate des pannes et l'analyse continue des métriques.
	
4. Qualification de charge
	- Utilisation conjointe de simulateurs d'injection de trafic et d'outils de capture métrique pour anticiper les pics de charge.

### Protocole de gestion des incidents (Cycle ITIL)
1. Qualification et traitement opérationnel
	- Enregistrement (`Incident Logging`), affectation (`Incident Assignment`) et priorisation (`Incident Prioritization`).
	- Suivi d'état (`Incident Tracking`), résolution et clôture (`Incident Closure`).
	
2. Post-mortem sans blâme
	- **Impératif** : Analyse systématique des causes profondes après chaque panne pour concevoir des actions préventives pérennes.

---
## Boîte à Outils de l'Ingénieur SRE

### Outillage technique par domaine
1. Surveillance et centralisation des logs
	- Tableaux de bord, collecte et analyse : **Prometheus**, **Grafana**, **Kibana**, **Splunk**, **Nagios**, **Dotcom-Monitor**.
	
2. Automatisation et gestion de configuration
	- Chaînes CI/CD : **Jenkins**, **GitLab CI**, **CircleCI**, **TeamCity**.
	- Infrastructure as Code (IaC) et orchestration système : **Terraform**, **Ansible**, **Puppet**, **Chef**, **SaltStack**, **Docker**, **Git**.
	
3. Tests de charge et de stress
	- Outils d'injection de trafic : **Apache JMeter**, **k6**, **Gatling**, **Locust**, **Artillery**, **ApacheBench**, **Siege**, **Wrk**, **Tsung**, **Drill**.
	
4. Sécurité opérationnelle
	- Contrôles d'accès, gestion stricte des identités (IAM) et chiffrement de bout en bout des flux.
	- **Restriction** : La sélection d'outils de sécurité performants requiert une expertise pointue pour éviter les failles de configuration.
	
5. Pilotage et coordination projet
	- Outils collaboratifs de suivi : **Jira Software**, **Trello**, **Monday.com**, **Asana**, **Citrix Podio**, **ProofHub**, **Teamwork**.

---
## Retours d'Expérience et Cas d'Usage Industriels

### Google (Panne Google Drive 2017)
- **Incident** : Défaillance majeure impactant des millions d'utilisateurs.
- **Résolution SRE** : Mise en œuvre d'un monitoring accéléré, reconfiguration des mécanismes d'alerte et formalisation de processus de post-mortem pour éradiquer la récurrence de la panne.

### Spotify (Instabilité applicative 2018)
- **Incident** : Crashes répétés et fermetures inopinées de l'application cliente.
- **Résolution SRE** : Détection d'une fuite de mémoire au sein de la bibliothèque de télémétrie, refonte de la gestion mémoire du composant défaillant et validation par des tests de charge rigoureux.

### Netflix (Coupure de flux de streaming 2017)
- **Incident** : Erreurs de connexion massives et indisponibilité de la diffusion.
- **Résolution SRE** :
	- Identification rapide d'une anomalie sur le réseau de distribution de contenu (**CDN**).
	- **Astuce** : Exécution d'un `rollback` vers la dernière version stable du composant de supervision et mise en place d'un routage de secours vers des serveurs alternatifs.

---
## Évolutions et Tendances Futures du SRE

### Axes d'évolution technologique
1. Standardisation de Kubernetes
	- Adoption massive pour les architectures distribuées cloud-natives imposant une maîtrise experte de l'orchestration de conteneurs.
	
2. Généralisation de l'observabilité
	- Déploiement étendu de traceurs distribués (**Jaeger**, **Zipkin**) couplés aux métriques pour accélérer le diagnostic complexe.
	
3. Intégration de l'intelligence artificielle (AIOps)
	- Utilisation de modèles de Machine Learning pour la détection temps réel d'anomalies et l'automatisation prédictive des actions correctives.
	
4. Sécurité native (DevSecOps / SRE)
	- Renforcement des politiques de chiffrement, contrôle des accès et résilience structurelle face aux cyberattaques.
	
5. Synergie étroite SRE et DevOps
	- Coopération opérationnelle accrue pour conjuguer rythme de livraison élevé et stabilité maximale des architectures logicielles.
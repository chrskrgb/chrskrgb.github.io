---
title: Méthodes Agiles & Gestion de Projet
tags:
  - Document
  - DevOps
  - IT
  - Méthode
  - Agile
  - Scrum
---

# Méthodes Agiles & Gestion de Projet

## Gestion de projet en cascade (Waterfall)

### Concept et utilité
- **Définition** : Modèle de gestion prédictif fondé sur un séquencement linéaire des activités.
- **Rôle et avantages** :
	- Prévisibilité organisationnelle et structure méthodologique rigoureuse.
	- Clarté des dépendances et facilité d'estimation budgétaire initiale.
	- Maîtrise contractuelle stricte axée sur le cahier des charges.

### Étapes séquentielles et limites
1. Phases séquentielle 
	- **Spécifications** : Formalisation exhaustive des exigences client.
	- **Conception générale** : Architecture macroscopique du système.
	- **Conception détaillée** : Spécifications techniques des sous-ensembles.
	- **Codage** : Implémentation logicielle.
	- **Intégration** : Assemblage des briques logicielles.
	- **Mise en production** : Déploiement final pour l'utilisateur.
	- **Maintenance** : Suivi post-livraison.
	
2. Facteurs de risque opérationnels
	- **Restriction** : Incapacité structurelle d'adaptation face aux imprévus et modifications d'exigences.
	- **Effet critique** : Un décalage sur une étape entraîne une cascade de retards bloquants sur les phases suivantes.
	- **Dette technique** : Traitement pénible des correctifs en phase d'intégration tardive.
	- **Feedback** : Retours utilisateurs très tardifs induisant un risque de non-conformité au besoin réel.

---
## Approche Agile

### Rôle et avantages
- **Définition** : Philosophie de gestion basée sur des cycles itératifs et incrémentaux, intégrant la planification à l'exécution.
- **Avantage principal** : Alignement continu du produit sur la valeur métier grâce à des boucles de rétroaction rapides.

### Principes et arbitrage méthodologique
1. Piliers directeurs
	- **Priorisation par la valeur** : Ordonnancement des fonctionnalités selon leur importance client.
	- **Livraisons incrémentales régulières** : Production fréquente de versions opérationnelles à fort impact.
	- **Volatilité acceptée** : Ajustements continus intégrant le changement au lieu de le subir.
	- **Collaboration étroite** : Échanges constants et directs (face à face) entre commanditaires et développeurs.
	
2. Compromis opérationnels
	- **Gains** : Détection précoce des anomalies, accélération du time-to-market et forte responsabilisation des équipes.
	- **Restriction** : Traçabilité complexe des dépendances inter-projets et prévision budgétaire globale difficile.
	- **Contrainte** : Courbe d'apprentissage exigeante et gestion des dépendances techniques en flux tendu.

---
## Cadre méthodologique Scrum

### Concept et utilité
- **Définition** : Cadre (framework) agile itératif structuré en itérations courtes de **Sprints** (durée de 2 à 4 semaines).
- **Valeurs fondamentales** : **Engagement**, **Focus** (Attention), **Ouverture**, **Respect** et **Courage**.

### Rôles de la Scrum Team
- **Règle absolue** : Absence de hiérarchie ou de sous-équipes ; équipe pluridisciplinaire et entièrement auto-organisée alignée sur un unique **Objectif de Produit**.
1. **Product Owner** (`PO`)
	- Maximisation de la valeur du produit délivré par l'équipe.
	- Formalisation, explicitation et priorisation continue du **Product Backlog**.
	- Validation de la bonne compréhension des items par les développeurs.
	
2. **Scrum Master** (`SM`)
	- Garant de l'application et de l'efficacité du cadre **Scrum**.
	- Suppression proactive de tous les obstacles entravant la progression de l'équipe.
	- Coaching de la cellule vers l'auto-gestion et la pluridisciplinarité.
	- **Impératif** : Maintien strict du cadre temporel (**Timebox**) de chaque événement Scrum.
	
3. **Developers**
	- Prise en charge collective de la conception, de la réalisation et des tests techniques.
	- Engagement à livrer un incrément respectant la norme de conformité établie.

### Cérémonies de synchronisation
1. **Sprint Planning**
	- Sélection des items du **Product Backlog** et engagement sur l'objectif du sprint à venir.
	
2. **Daily Scrum** (Mêlée quotidienne)
	- Point de synchronisation quotidien court pour évaluer l'avancement et lever les blocages.
	
3. **Sprint Review**
	- Démonstration de l'incrément fonctionnel aux parties prenantes en fin d'itération.
	
4. **Sprint Retrospective**
	- Analyse du fonctionnement interne de l'équipe pour identifier et appliquer des améliorations immédiates.

### Artefacts et engagements associés
1. **Product Backlog**
	- Inventaire exhaustif et dynamique de toutes les fonctionnalités souhaitées (**User Stories**).
	- **Engagement** : Doit servir directement l'**Objectif de Produit**.
	
2. **Sprint Backlog**
	- Sous-ensemble d'items sélectionnés et planifiés pour le sprint en cours.
	- **Engagement** : Doit servir directement l'**Objectif de Sprint**.
	
3. **Incrément**
	- Somme des éléments du backlog achevés durant le sprint et opérationnels.
	- **Règle absolue** : Tout incrément doit respecter impérativement la **Definition of Done** (`DoD`).

---
## Méthodes Agiles complémentaires et Outillage

### Variantes méthodologiques
1. **Kanban**
	- Approche visuelle orientée flux tiré et livraison continue sans sprints prédéfinis.
	- Suivi d'état par colonnes (`To Do`, `Doing`, `Done`).
	- **Règle absolue** : Limitation stricte du travail en cours (**WIP** - Work In Progress) pour résorber les goulots d'étranglement.
	- Usage recommandé : Équipes soumises à un flux de travail continu et hautement imprévisible.
2. **Extreme Programming** (`XP`)
	- Cadre centré sur l'excellence technique et la qualité logicielle pure.
	- Pratiques d'ingénierie : Développement piloté par les tests (**TDD**), programmation en binôme (**Pair Programming**) et refactorisation continue.

### Outils de pilotage agile
1. **Jira Software** : Gestion avancée de backlogs, suivi de vélocité et paramétrage de tableaux Scrum/Kanban.
2. **GitHub** : Suivi des tâches techniques, gestion des revues de code et intégration directe aux dépôts de sources.
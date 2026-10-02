---
tags:
  - Document
  - ADDS
  - Admin
  - IT
  - Système
  - Microsoft
---
## Administration Avancée : FSMO et Relations d'Approbation

---
---
### Localisation, Transfert et Saisie des Rôles FSMO

1. Localisation des rôles FSMO
	- Via les consoles graphiques :
		- Rôles de domaine (RID, PDC, Infrastructure) : Dans **Utilisateurs et ordinateurs Active Directory**, clic droit sur le nom de domaine > *Maîtres d'opérations* (onglets *RID*, *CDP/PDC* et *Infrastructure*).
		- Rôle d'attribution des noms de domaine : Dans **Domaines et approbations Active Directory**, clic droit sur le nœud racine > *Maîtres d'opérations*.
		- Rôle de Maître de schéma :
			- **Impératif :** La console du schéma n'est pas enregistrée par défaut. Exécuter dans une invite de commandes administrateur :
				```cmd
				regsvr32.exe schmmgmt.dll
				```

- Lancer `mmc.exe`, ajouter le composant logiciel enfichable *Schéma Active Directory*, faire un clic droit sur le nœud racine > *Maîtres d'opérations*.
- En invite de commandes (requête globale) :
	```cmd
	netdom query /domain:domaine.lan fsmo
	```

2. Transfert des rôles FSMO
	- Méthode graphique :
		- Se connecter sur le DC de destination (ou utiliser le menu *Changer le contrôleur de domaine Active Directory* pour cibler le DC récepteur).
		- Ouvrir la console correspondant au rôle visé, accéder à la fenêtre *Maîtres d'opérations*, puis cliquer sur *Modifier*.
		- Valider la confirmation pour déclencher le transfert et la réplication réseau immédiate.
		
	- Méthode en invite de commandes avec `ntdsutil` :
		```cmd
		ntdsutil
		roles
		connections
		connect to server NomDuDCRecepteur
		quit
		transfer rid master
		transfer pdc
		transfer infrastructure master
		transfer domain naming master
		transfer schema master
		quit
		quit
		```

2. Transfert forcée des rôles FSMO (Seizing)
	- Concept et cas d'usage :
		- **Exception** : Procédure de dernier recours réservée exclusivement aux pannes matérielles définitives ou destructions irréversibles du DC hébergeant initialement le rôle FSMO.
		- Comportement de l'interface graphique en cas de panne : Les consoles renvoient systématiquement une erreur d'indisponibilité signalant l'impossibilité de contacter le propriétaire actuel du rôle.
		
	- **Règle** : Ne jamais reconnecter au réseau un contrôleur de domaine dont les rôles FSMO ont été saisis de force via la commande `seize`, sous peine de provoquer des corruptions de métadonnées et des incohérences irréversibles dans la base Active Directory.
	- Procédure de capture forcée via `ntdsutil` :
		```cmd
		ntdsutil
		roles
		connections
		connect to server NomDuDCRecepteur
		quit
		seize rid master
		seize pdc
		seize infrastructure master
		seize naming master
		seize schema master
		quit
		quit
		```
		
	- **Note technique** : Lors de l'exécution d'une commande `seize`, `ntdsutil` tente d'abord d'exécuter un transfert propre et sécurisé (`transfer`) ; en constatant l'échec de communication avec le serveur source, il bascule automatiquement en mode de saisie forcée.
	- **Validation** : Exécuter `netdom query /domain:domaine.lan fsmo` pour vérifier que le nouveau DC est reconnu comme propriétaire unique de l'ensemble des rôles.

---
### Relations d'Approbation Inter-Domaines et Inter-Forêts

1. **Paramètres de liaison d'approbation**
	- **Direction du flux d'accès** :
		- **Unidirectionnelle en entrée** : Les utilisateurs de la forêt source peuvent accéder aux ressources autorisées de la forêt locale.
		- **Unidirectionnelle en sortie** : Les utilisateurs du domaine local peuvent s'authentifier et accéder aux ressources autorisées du domaine distant.
		- **Bidirectionnelle** : Confiance symétrique et mutuelle permettant aux utilisateurs de chaque entité d'accéder aux ressources de l'autre selon leurs droits.
		
	- **Transitivité** :
		- **Transitive** : Approbation étendu en cascade à travers l'arborescence (**si le domaine A approuve le domaine B, et que B approuve le domaine C, alors A approuve implicitement le domaine C**).
		- **Non transitive** : Accès strictement cantonné aux deux domaines signataires directs de l'accord d'approbation.
		
	- **SID Filtering - Filtrage des SID** : Activé par défaut sur les approbations externes pour éliminer les identifiants de sécurité étrangers présents dans les jetons d'accès et contrer les attaques par usurpation de droits (`sIDHistory`).

2. **Approbations de forêts**
	- **Objectif** : Mutualiser les ressources et permettre l'authentification croisée entre deux forêts Active Directory totalement indépendantes (ex: lors d'une fusion d'entreprises ou d'un partenariat industriel).
	- **Impératif DNS :** Chaque forêt doit résoudre le nom complet de l'autre forêt. Configurer des **Redirecteurs conditionnels** bilatéraux dans les consoles DNS de chaque domaine racine, pointant vers l'adresse IP des serveurs DNS principaux distants.
	- Configuration via la console **Domaines et approbations Active Directory** :
		- Clic droit sur le domaine racine source > *Propriétés* > onglet *Approbations* > *Nouvelle approbation*.
		- Spécifier le nom du domaine racine distant, puis sélectionner le type **Approbation de forêt**.
		- Choisir la direction (*Bidirectionnelle*, *Sens unique en entrée*, *Sens unique en sortie*).
		- Périmètre de création : Cocher *Ce domaine et le domaine spécifié* pour configurer simultanément les deux côtés du lien (requiert la saisie d'identifiants *Admins de l'entreprise* de la forêt distante).
		- Définition du périmètre d'authentification :
			- *Authentification pour toutes les ressources de la forêt* : Accès réseau global.
			- *Authentification sélective* : Accès granulaire imposant d'accorder explicitement le droit *Autorisation d'authentification* sur les objets ordinateurs cibles.
			
	- Validation et gestion :
		- Utiliser le bouton *Valider* dans l'onglet *Approbations* pour vérifier l'intégrité de la liaison bilatérale.
		- Contrôler l'onglet *Routage des suffixes de noms* pour confirmer l'activation des chemins d'authentification.
		- **Restriction :** Les approbations entre forêts sont intransitives entre forêts tierces (si la Forêt A approuve la Forêt B, et que la Forêt B approuve la Forêt C, la Forêt A n'approuve en aucun cas la Forêt C).
		- Suppression : Pour démanteler une approbation, supprimer le lien dans l'onglet *Approbations* en cochant l'option de suppression bilatérale conjointe et en fournissant les identifiants du domaine distant.

3. **External Trusts - Approbations externes**
	- **Objectif** : Lier directement deux domaines spécifiques résidant dans des forêts distinctes sans établir d'accord d'approbation global à l'échelle de la forêt.
	- Caractéristiques :
		- Liens configurables en unidirectionnel ou bidirectionnel.
		- **Restriction :** Strictement **non transitives** : le lien de confiance ne s'étend sous aucun prétexte aux autres sous-domaines des forêts respectives.
		- Intègrent nativement le filtrage des SID pour neutraliser les risques d'élévation de privilèges.
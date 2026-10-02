---
tags:
  - Document
  - ADDS
  - Admin
  - IT
  - Système
  - Microsoft
---
## Fondamentaux et Composants Clés de l'Active Directory

---
---
### Rôle et Principes d'un Annuaire d'Entreprise

1. **But de l'annuaire**
	- **Définition** : Annuaire LDAP développé par Microsoft pour centraliser l'identification, l'authentification et l'attribution des droits dans un système d'information.
	- **Rôle principal** : Contrôler l'accès aux ressources partagées (fichiers, imprimantes, applications) selon les autorisations attribuées.
	- **Administration centralisée** : Gestion unifiée des objets, attribution des permissions et application de politiques de sécurité globales via les stratégies de groupe (GPO).
	- **Authentification unique** : Un compte unique permet l'accès à l'ensemble des ressources autorisées du réseau.
	- **Identification unique** : Référencement exhaustif des objets (utilisateurs, groupes, machines) facilitant la révocation rapide des accès.
	- **Base de données relationnelle** : Répertoire unique pour le déploiement de logiciels et la gestion des configurations.

2. **Modèles d'architecture** : Groupe de travail vs Domaine
	- **Groupe de travail - Workgroup** :
		- Repose sur la base **SAM** locale de chaque machine physique.
		- **Restriction :** Inadapté aux environnements d'entreprise en raison de la multiplication des comptes locaux et l'absence totale d'administration centralisée.
		
	- **Domaine** :
		- Centralise les données d'identité et de sécurité au sein d'une base unique répliquée entre les serveurs.
		- Permet une connexion unifiée et transversale pour accéder aux ressources distantes.

3. **Rôles Active Directory**
	- **AD DS - Active Directory Domain Services** : Rôle fondamental, instancie l'annuaire, la gestion des identités, l'authentification et l'attribution des droits.
	- **AD CS - Active Directory Certificate Services** : Infrastructure de gestion de clés et délivrance de certificats numériques (PKI).
	- **AD FS - Active Directory Federation Services** : Authentification fédérée et mécanisme de délégation d'identité (SSO) via jetons de sécurité.
	- **AD RMS - Active Directory Rights Management Services** : Protection granulaire des fichiers (chiffrement, interdiction d'impression ou de copie) couplée aux applications clientes compatibles.
	- **AD LDS - Active Directory Lightweight Directory Services** : Annuaire LDAP léger en mode instance sans contrôleur de domaine, adapté aux référentiels applicatifs et extranets.
		- **Restriction :** Inutilisable pour le contrôle d'accès direct aux ressources Windows en raison de l'absence des primitives de sécurité propres à un domaine Active Directory.

---
### Structure Logique et Partitions d'Annuaire

1. **Composants logiques de la hiérarchie**
	- **Classes et attributs** : Définis au sein du schéma pour modéliser les entités de l'annuaire.
	- **OU - Unités d'organisation** : Conteneurs hiérarchiques permettant de structurer les objets, de déléguer l'administration et d'appliquer des GPO.
	- **Domaine** : Limite administrative et sécurisée regroupant des objets (utilisateurs, ordinateurs, groupes, OU).
	- **Domaine racine** : Premier domaine déployé, constituant le socle logique initial de l'infrastructure.
	- **Domaines enfants** : Sous-domaines hiérarchiques dérivés d'un domaine parent.
	- **Arborescence (Arbre)** : Ensemble continu de domaines partageant un espace de nommage DNS contigu (ex: `sub.domaine.lan` ou `paris.domaine.lan` au sein de `domaine.lan`).
	- **Forêt** : Périmètre de sécurité ultime regroupant un ou plusieurs arbres aux espaces de noms disjoints.
		- Partage obligatoire d'une partition de schéma commune.
		- Partage d'une partition de configuration et d'un Catalogue Global uniques.
		- Établissement automatique et bidirectionnel de relations d'approbation transitives entre ses domaines.

2. **Schéma Active Directory**
	- **Définition** : Modèle formel répertoriant l'ensemble des classes d'objets (`classSchema`) et des attributs (`attributeSchema`) autorisés.
	- **Évolution** : Extensible pour satisfaire les besoins d'applications tierces (ex: Microsoft Exchange).
	- **Règle** : Les modifications apportées au schéma sont irréversibles et réservées exclusivement aux membres du groupe de sécurité **Administrateurs du schéma**.

3. **Partitions de la base NTDS.DIT (Naming Contexts)**
	- **Partition de Schéma** :
		- Contient la définition universelle des classes et attributs.
		- Unique par forêt et répliquée sur l'ensemble des contrôleurs de la forêt.
		- Accessible via la console **Schéma Active Directory** ou **Modification ADSI**.
		
	- **Partition de Configuration** :
		- Stocke la topologie physique et logique de l'infrastructure (sites AD, sous-réseaux, liaisons de sites, services d'annuaire).
		- Unique par forêt et répliquée sur toute la forêt.
		- Accessible via **Sites et services Active Directory** et **Modification ADSI** (contexte Configuration, sous-dossier `CN=Partitions`).
		
	- **Partition de Domaine** :
		- Contient l'ensemble des objets spécifiques au domaine (utilisateurs, ordinateurs, groupes, OU).
		- Répliquée exclusivement sur les contrôleurs du domaine local.
		- Accessible via **Utilisateurs et ordinateurs Active Directory** et **Modification ADSI**.
		
	- **Partitions applicatives DNS (intégrées à l'AD)** :
		- `ForestDnsZones` (`DC=ForestDnsZones,DC=...`) : Contient les zones DNS répliquées à l'échelle de la forêt (dont la zone technique `_msdcs`).
		- `DomainDnsZones` (`DC=DomainDnsZones,DC=...`) : Contient le dossier `CN=MicrosoftDNS` stockant les enregistrements de la zone DNS du domaine local.

---
### Protocoles Fondamentaux et Système DNS

1. **Protocoles de communication et d'authentification**
	- **LDAP / LDAPS** :
		- Point d'entrée pour la consultation, l'ajout et l'administration des objets de l'annuaire.
		- **Port standard** : TCP 389.
		- **Port sécurisé (LDAPS)** : TCP 636 via chiffrement SSL/TLS.
		- **Signature LDAP** : Assure l'intégrité et l'authentification des requêtes via SASL Bind pour contrer l'usurpation.
		
	- **Kerberos v5** :
		- Protocole d'authentification réseau à clé symétrique reposant sur le Centre de distribution de clés (**KDC**) hébergé sur chaque DC.
		- **Service AS - Authentication Service** : Valide les identifiants initiaux et délivre un ticket **TGT** (durée de validité recommandée : 10 heures).
		- **Service TGS - Ticket-Granting Service** : Échange le TGT contre un ticket de service (**TGS**) pour accéder à une ressource spécifique.
		- **Chiffrement** : Supporte les algorithmes AES 128/256 bits (apparu avec Windows Server 2008), ainsi que DES.
		- **Restriction :** Si le service KDC est indisponible, toute nouvelle tentative d'authentification réseau échoue globalement.
		- **Règle : Désactiver le protocole hérité NTLM ou restreindre son usage strict à NTLMv2 en raison de ses vulnérabilités structurelles.

2. **Intégration DNS et zones techniques**
	- **Impératif :** L'annuaire Active Directory exige une infrastructure DNS opérationnelle prenant en charge l'enregistrement dynamique pour localiser les services et les contrôleurs de domaine.
	- **Zone technique `_msdcs.[domaine]`** :
		- `dc` : Paramètres des sites et localisation des contrôleurs de domaine.
		- `domains` : Liste des contrôleurs par domaine (GUID de domaine).
		- `gc` : Enregistrements SRV pour la localisation des serveurs de Catalogue Global.
		- `pdc` : Enregistrements SRV ciblant le détenteur du rôle Émulateur PDC.
		
	- **Zone standard de domaine (`[domaine]`)** :
		- Contient les enregistrements d'hôtes (A, AAAA) et de services (SRV) automatisés pour l'ensemble des machines et services du domaine.

---
### Contrôleurs de Domaine et Rôles Spécialisés

1. **DC - Contrôleur de domaine standard**
	- Serveur hébergeant une copie complète en lecture/écriture de la base de données NTDS (`C:\Windows\NTDS\ntds.dit`).
	- Héberge le partage réseau système **SYSVOL** (`C:\Windows\SYSVOL`), qui stocke et distribue les **stratégies de groupe (GPO)** et les **scripts**.
	- **Impératif :** Déployer au minimum deux contrôleurs de domaine par environnement pour garantir la haute disponibilité, la répartition de charge et la tolérance aux pannes.

2. **RODC - Contrôleur de domaine en lecture seule**
	- Introduit avec Windows Server 2008.
	- Héberge une copie intégrale de l'annuaire en lecture seule.
	- Conçu pour les **sites distants** ou succursales présentant un **faible niveau de sécurité physique** : réduit la latence d'authentification tout en protégeant l'annuaire central en cas de compromission physique de la machine.
	- Restreint la mise en cache des mots de passe aux seuls utilisateurs explicitement autorisés.

3. **GC - Catalogue Global**
	- Contrôleur de domaine hébergeant une copie complète des objets de son domaine local en lecture/écriture, ainsi qu'une réplique partielle en lecture seule de tous les objets de la forêt (**PAS - Partial Attribute Set**).
	- Optimise les requêtes de recherche inter-domaines et accélère l'accès réseau sans saturer les liaisons WAN.
	- Requis pour le fonctionnement d'applications dépendantes telles que **Microsoft Exchange** ou **MSMQ**.
	- **Directives d'implantation** :
		- **Mono-domaine** : Activer le rôle de Catalogue Global sur tous les contrôleurs de domaine (charge minime, disponibilité maximale).
		- **Multi-domaines** :
			- **Impératif :** Déployer au moins un Catalogue Global par site géographique regroupant une centaine d'utilisateurs ou exploitant des applications comme Microsoft Exchange.
			- **Astuce :** Activer la fonction **Mise en cache de l'appartenance au groupe universel** (UGMC) sur les sites distants dépourvus de GC afin d'économiser la bande passante WAN lors des ouvertures de session.

4. **Rôles maîtres d'opérations (FSMO - Flexible Single Master Operation)**
	- **Concept** : Fonctions d'autorité exclusives attribuées à un unique DC afin de prévenir les conflits de modifications concurrentes dans un modèle multimaître.
	- **Rôles uniques** à l'échelle de la **forêt (2 rôles)** :
		- **Maître de schéma** (Schema Master) : Contrôle et applique toute modification structurelle apportée au schéma Active Directory. Requiert l'appartenance au groupe *Administrateurs du schéma*.
		- **Maître d'attribution des noms de domaine** (Domain Naming Master) : Valide l'ajout, le renommage et la suppression de domaines ou de partitions applicatives dans la forêt.
		
	- **Rôles uniques** à l'échelle de chaque **domaine (3 rôles)** :
		- **RID Master - Maître RID** : Alloue des blocs séquentiels de 500 identifiants relatifs (RID) à chaque DC pour générer des SID (`objectSID`) uniques.
			- **Restriction :** En cas d'indisponibilité prolongée du maître RID, tout DC ayant épuisé son pool local de RID se retrouve dans l'incapacité de créer de nouveaux objets de sécurité.
			
		- **PDC Emulator - Émulateur PDC** :
			- Agit comme serveur de temps de référence (**NTP**) pour le domaine (vital pour la **synchronisation Kerberos**).
			- Traite en priorité les réplications immédiates de modifications de mot de passe et gère les verrouillages de compte.
			- Centralise la modification des objets GPO pour éviter les conflits d'édition.
			
		- **Infrastructure Master - Maître d'infrastructure** : Met à jour les références inter-domaines via les objets fantômes (synchronisation des couples GUID/SID/DN lors des appartenances de groupes inter-domaines).
			- Inutile si le domaine ne compte qu'un seul DC ou si tous les DC du domaine détiennent également le rôle de Catalogue Global.

---
### Architecture du Partage Réseau SYSVOL

1. **Rôle et structure des dossiers**
	- **Emplacement local par défaut** : `C:\Windows\SYSVOL\sysvol\[NomDomaine]`.
	- Partage réseau répliqué entre tous les contrôleurs de domaine, accessible via `\\[domaine]\SYSVOL`.
	- **Sous-répertoires critiques** :
		- `Policies` : Stocke les conteneurs de stratégies de groupe (GPO), indexés par leur identifiant GUID.
		- `scripts` : Fichiers batch (`.bat`, `.cmd`), exécutables et scripts de démarrage ou de connexion.
		- `staging` : Zone tampon d'attente pour le moteur de synchronisation.

2. **Moteurs de réplication et permissions**
	- **Moteur de réplication** : Historiquement géré par **FRS - File Replication Service**, remplacé par **DFSR - Distributed File System Replication** depuis Windows Server 2008.
	- **Restriction :** Ne jamais altérer manuellement les autorisations de sécurité NTFS par défaut accordées au groupe *Utilisateurs authentifiés* sur le partage SYSVOL, sous peine de bloquer l'application des stratégies de groupe sur les clients.
	- **Règle** : Proscrire tout stockage de données personnelles ou applicatives volumineuses dans SYSVOL pour éviter d'engorger la réplication inter-serveurs.

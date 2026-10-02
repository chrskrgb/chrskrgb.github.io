---
tags:
  - Document
  - ADDS
  - Admin
  - IT
  - Système
  - Microsoft
---
## Outils d'Administration et Consoles Système

---
---
### Consoles Graphiques et d'Exploration LDAP

1. **Console Utilisateurs et ordinateurs Active Directory (`dsa.msc`)**
	- Console centrale pour l'administration quotidienne des identités et des machines.
	- Conteneurs et dossiers affichés par défaut :
		- `Builtin` : Groupes de sécurité locaux par défaut créés par le système.
		- `Computers` : Conteneur par défaut accueillant les machines clientes jointes au domaine.
		- `Domain Controllers` : Conteneur par défaut hébergeant les contrôleurs de domaine.
		- `ForeignSecurityPrincipals` : Objets représentant des identités de sécurité provenant de domaines externes approuvés.
		- `Managed Service Accounts` : Comptes de service administrés (gMSA) bénéficiant d'une rotation automatique des mots de passe.
		- `Users` : Conteneur par défaut des utilisateurs et groupes intégrés du domaine.
		
	- **Fonctionnalités avancées** (**Menu *Affichage*** > ***Fonctionnalités avancées***) :
		- Démasque les dossiers système cachés (`System`, `Keys`, `LostAndFound`, `Program Data`).
		- Ajoute les onglets critiques dans les propriétés des objets : **Objet** (protection contre la suppression accidentelle), **Sécurité** (gestion détaillée des ACL) et **Éditeur d'attributs** (modification brute des attributs LDAP).
		
	- Actions sur le domaine (clic droit sur la racine du domaine) :
		- Lancer la *Délégation de contrôle*.
		- *Rechercher* des objets dans l'annuaire via filtres avancés.
		- *Changer de contrôleur de domaine Active Directory* ou *Changer de domaine*.
		- *Augmenter le niveau fonctionnel du domaine*.
		- Consulter et transférer les *Maîtres d'opérations* FSMO de niveau domaine.
		- Afficher le *Jeu de stratégie résultant* (RSoP).

2. **ADAC - Centre d'administration Active Directory**
	- Introduite avec Windows Server 2008 R2, modernisée avec Windows Server 2012. Reposee sur le moteur PowerShell.
	- Panneau de l'historique Windows PowerShell :
		- Affiche en temps réel l'ensemble des commandes PowerShell exécutées en arrière-plan par les clics de l'interface graphique (cocher *Afficher tout*).
		- Charge automatiquement le module PowerShell Active Directory (`Import-Module ActiveDirectory`).
		
	- Gestion ergonomique unifiée :
		- Création simplifiée des utilisateurs, groupes et unités d'organisation avec activation par défaut de la case *Protéger contre la suppression accidentelle*.
		- Intégration de la **Corbeille Active Directory** pour la restauration immédiate des objets supprimés avec préservation de l'ensemble de leurs liaisons et attributs.
		- Prise en charge du **DAC - Contrôle d'accès dynamique** : Gestion des autorisations de ressources basée sur des revendications et des conditions contextuelles (ex: machine source, conformité réseau).
		- Administration des **Stratégies d'authentification** et des **Silos de stratégies d'authentification** (Windows Server 2012 R2).

3. **Modification ADSI (`adsiedit.msc`)**
	- Éditeur LDAP de bas niveau permettant de parcourir et modifier directement les objets et attributs au sein de chaque partition (Naming Context) de l'Active Directory.
	- Permet de se connecter explicitement à :
		- La partition par défaut du domaine.
		- La partition de Configuration (`CN=Configuration,DC=...`).
		- La partition de Schéma (`CN=Schema,CN=Configuration,DC=...`).
		- Les partitions applicatives DNS (`DC=ForestDnsZones,...` et `DC=DomainDnsZones,...`).
		
	- **Règle** : À manipuler avec une extrême précaution : aucune validation de cohérence n'est effectuée lors de l'édition directe des attributs d'annuaire via ADSI Edit.

4. **Outil** d'exploration en **mode texte `ldp.exe`**
	- Utilitaire graphique bas niveau pour interroger l'annuaire Active Directory via des primitives de requêtes LDAP brutes.
	- Connexion et liaison (Bind) :
		- Menu *Connexion* > *Se connecter* : Spécifier le serveur DNS et le port TCP 389 (ou 636 pour SSL).
		- Affiche les métadonnées du DC (dont `domainFunctionality` et `forestFunctionality` sous forme numérique, ex: `7` pour Windows Server 2016).
		- Menu *Connexion* > *Lier* : Authentification avec les identifiants de session ou un compte explicite.
		
	- Exploration de l'arborescence :
		- Menu *Affichage* > *Arborescence* : Permet de renseigner un DN de base pour explorer la structure textuelle de n'importe quelle partition.
		- **Astuce :** Utiliser `ldp.exe` pour repérer rapidement le chemin absolu DN des partitions applicatives DNS (`ForestDnsZones`, `DomainDnsZones`) afin de les charger sans erreur dans la console *Modification ADSI*.

---
### Variables d'Environnement Windows et Scripts

1. **Variables d'environnement standard**
	- **Systèmes et chemins de fichiers** :
		- `%SystemDrive%` : Lecteur racine du système d'exploitation (ex: `C:`).
		- `%SystemRoot%` / `%WinDir%` : Dossier principal du système Windows (`C:\Windows`).
		- `%ProgramFiles%` : Répertoire des applications 64 bits (`C:\Program Files`).
		- `%ProgramFiles(x86)%` : Répertoire des applications 32 bits sous OS 64 bits (`C:\Program Files (x86)`).
		- `%ProgramW6432%` : Répertoire des applications 64 bits (utilisé en contexte d'émulation 32 bits).
		- `%CommonProgramFiles%` : Fichiers partagés des applications 64 bits (`C:\Program Files\Common Files`).
		- `%CommonProgramFiles(x86)%` : Fichiers partagés des applications 32 bits (`C:\Program Files (x86)\Common Files`).
		- `%CommonProgramW6432%` : Fichiers partagés 64 bits accessibles depuis un processus 32 bits.
		
	- **Profils utilisateurs et données** :
		- `%UserProfile%` : Répertoire racine du profil de l'utilisateur connecté (`C:\Users\NomUtilisateur`).
		- `%HomeDrive%` : Lettre du lecteur associé au répertoire personnel de l'utilisateur (ex: `C:`).
		- `%HomePath%` : Chemin du profil de l'utilisateur sans la lettre de lecteur (ex: `\Users\NomUtilisateur`).
		- `%AppData%` : Données d'applications itinérantes (`C:\Users\NomUtilisateur\AppData\Roaming`).
		- `%LocalAppData%` : Données d'applications locales non répliquées (`C:\Users\NomUtilisateur\AppData\Local`).
		- `%ProgramData%` / `%AllUsersProfile%` : Données d'applications communes à tous les profils (`C:\ProgramData`).
		- `%Public%` : Répertoire public partagé (`C:\Users\Public`).
		- `%OneDrive%` : Chemin absolu vers le dossier OneDrive de l'utilisateur (si installé).
		
	- **Matériel et architecture** :
		- `%COMPUTERNAME%` : Nom NetBIOS de la machine sur le réseau.
		- `%USERNAME%` : Nom du compte utilisateur actif dans la session.
		- `%USERDOMAIN%` : Nom du domaine ou de la machine locale auquel appartient l'utilisateur actif.
		- `%USERDOMAIN_ROAMINGPROFILE%` : Nom du domaine gérant le profil itinérant de l'utilisateur.
		- `%PROCESSOR_ARCHITECTURE%` : Architecture de la puce processeur (`AMD64`, `x86`, `ARM64`).
		- `%PROCESSOR_IDENTIFIER%` : Description technique textuelle du processeur.
		- `%PROCESSOR_LEVEL%` : Numéro de modèle du processeur fourni par le fabricant.
		- `%PROCESSOR_REVISION%` : Numéro de révision matérielle du processeur.
		- `%NUMBER_OF_PROCESSORS%` : Nombre total de cœurs logiques / threads processeur disponibles.
		
	- **Système et exécution** (CMD) :
		- `%ComSpec%` : Chemin absolu de l'interpréteur de commandes (`C:\Windows\System32\cmd.exe`).
		- `%OS%` : Nom générique du noyau du système d'exploitation (`Windows_NT`).
		- `%Path%` : Liste ordonnée des chemins contenant les exécutables système et tiers.
		- `%PathExt%` : Extensions de fichiers considérées comme exécutables par le système (ex: `.EXE;.BAT;.CMD;.VBS`).
		- `%PROMPT%` : Chaîne de formatage visuel de l'invite de commandes (généralement `$P$G`).
		- `%PSModulePath%` : Chemins de recherche des modules de scripts Windows PowerShell.
		- `%LOGONSERVER%` : Nom du contrôleur de domaine (ou de la machine locale) ayant validé l'authentification de la session active.
		- `%SessionName%` : Type de session active (`Console` pour un accès physique direct, ou identifiant de session RDP distant).
		
	- **Fichiers temporaires** :
		- `%TEMP%` / `%TMP%` : Répertoire de stockage des fichiers temporaires de l'utilisateur (`C:\Users\NomUtilisateur\AppData\Local\Temp`).

2. **Variables dynamiques de l'interpréteur de commandes** (CMD)
	- **Non listées** par la commande `set` :
		- `%CD%` : Chemin absolu du répertoire de travail courant.
		- `%DATE%` : Date actuelle formatée selon les paramètres régionaux du système.
		- `%TIME%` : Heure courante formatée selon les paramètres régionaux du système.
		- `%RANDOM%` : Génère un entier pseudo-aléatoire compris entre `0` et `32767`.
		- `%ERRORLEVEL%` : Code numérique de retour renvoyé par la dernière commande exécutée.
		- `%CMDCMDLINE%` : Ligne de commande exacte ayant démarré la session `cmd.exe` active.
		- `%CMDEXTVERSION%` : Numéro de version des extensions de l'interpréteur de commandes courant.

3. **Commandes** et **utilisation** dans les **scripts**
	- Gestion en invite de commandes :
		- Afficher toutes les variables : Exécuter `set`.
		- Définir une variable temporaire (valable dans la session courante) : `set NOM=valeur`.
		- Définir une variable permanente (écrite dans le registre) : `setx NOM "valeur"`.
		
	- Syntaxe en script Batch (`.bat` / `.cmd`) :
		- Les variables sont encadrées par des symboles `%` (ex: `%USERNAME%`).
			```cmd
			@echo off
			:: Utilisation d'une variable système existante
			echo Bonjour %USERNAME%, votre dossier systeme est %SystemRoot%.
			
			:: Création et utilisation d'une variable locale au script
			set DOSSIER_CIBLE=D:\Sauvegardes
			echo Copie des fichiers vers %DOSSIER_CIBLE%...
			```
	
	- Syntaxe en script PowerShell (`.ps1`) :
		- Les variables d'environnement sont préfixées par `$env:` (ex: `$env:USERNAME`).
			```powershell
			# Utilisation d'une variable système existante
			Write-Output "Bonjour $env:USERNAME, votre dossier systeme est $env:SystemRoot."
			
			# Création et utilisation d'une variable d'environnement locale
			$env:DOSSIER_CIBLE = "D:\Sauvegardes"
			Write-Output "Copie des fichiers vers $env:DOSSIER_CIBLE..."
			```

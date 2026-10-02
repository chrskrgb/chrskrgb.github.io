---
tags:
  - Document
  - ADDS
  - Admin
  - IT
  - Système
  - Microsoft
---
## Modélisation, Objets et Sécurité dans l'Annuaire

---
---
### Typologie et Identifiants des Objets

1. **Identifiants uniques d'objets**
	- **Distinguished Name (DN)** : Chemin hiérarchique absolu de l'objet dans l'arborescence LDAP (ex: `CN=Chris,OU=Admin,DC=domaine,DC=lan`). Modifié automatiquement lors d'un déplacement ou d'un renommage.
	- **ObjectGUID** : Identifiant unique universel sur 128 bits attribué dès la création de l'objet ; strictement immuable dans toute la forêt.
	- **objectSID** : Identifiant de sécurité unique exploité par le système d'exploitation Windows pour le contrôle d'accès et l'attribution des droits (ACL).
	- **sAMAccountName** : Identifiant de connexion historique (pré-Windows 2000), limité à 20 caractères et unique au sein d'un même domaine.
	- **UserPrincipalName (UPN)** : Identifiant de connexion principal sous forme de messagerie (`utilisateur@domaine.lan`).

2. **Composants de syntaxe LDAP (Chemin DN)**
	- **`cn` (Common Name)** : Désigne le nom individuel de l'objet ciblé (utilisateur, groupe, contact, ordinateur).
	- **`ou` (Organizational Unit)** : Désigne un conteneur logique hiérarchique conçu pour la gestion administrative et l'application des GPO.
	- **`dc` (Domain Component)** : Composant constitutif du domaine Active Directory.

3. **Attributs clés d'administration**
	- **`adminCount`** : Prend la valeur `1` pour les comptes protégés par le mécanisme AdminSDHolder (comptes à privilèges administratifs), `0` pour les autres.
	- **`userAccountControl`** : Masque binaire définissant l'état du compte (activé, désactivé, verrouillé, mot de passe n'expire jamais).
	- **`pwdLastSet`** : Horodatage système de la dernière réinitialisation du mot de passe.
	- **`accountExpires`** : Date limite de validité du compte utilisateur.
	- **`logonCount`** : Compteur du nombre total d'authentifications réussies réalisées par l'objet.
	- **`lastLogonTimestamp`** : Horodatage de connexion répliqué périodiquement entre les contrôleurs de domaine (depuis Windows Server 2003).

---
### Gestion des Utilisateurs et Postes de Travail

1. **Administration des comptes utilisateurs**
	- **Attributs généraux** : Prénom, nom, bureau, téléphone, adresse e-mail, organisation, affectation hiérarchique (gestionnaire).
	- **Paramètres du compte** :
		- **Nom d'ouverture de session** (UPN et sAMAccountName).
		- **Horaires d'accès** : Plages horaires hebdomadaires autorisées pour la connexion.
		- **Se connecter à** : Restriction d'accès à des postes clients spécifiquement listés.
		- **Expiration** : Date de fin de contrat ou validité indéfinie.
		- **Options de mot de passe** : Exiger le changement à la prochaine session (recommandé en production), interdiction de modifier le mot de passe, mot de passe n'expire jamais (recommandé pour les tests/services).
		- **Sécurité renforcée** : Obligation d'authentification par carte à puce, protection contre la délégation Kerberos, sélection des algorithmes de chiffrement autorisés (DES, AES 128, AES 256).
		
	- **Profils et dossiers personnels** :
		- Chemin du profil (pour les profils itinérants).
		- Script d'ouverture de session (fichiers situés dans le partage `NETLOGON` / `SYSVOL`).
		- Chemin du dossier de base (lecteur réseau mappé ou chemin local).
		
	- **Accès distants et Bureau à distance (RDS)** :
		- **Appel entrant** : Autoriser, refuser ou contrôler l'accès via les stratégies réseau (VPN / NPS), vérification de l'appelant, rappel et attribution d'IP statique.
		- **Sessions** : Délais d'expiration des sessions actives, inactives ou déconnectées.
		- **Contrôle à distance** : Autoriser l'interaction ou la consultation seule avec ou sans l'accord de l'utilisateur.
		- **Profil des services Bureau à distance** : Chemin de profil dédié aux hôtes de session RDS et interdiction éventuelle de connexion.

2. **Création et redirection en ligne de commandes**
	- Créer un utilisateur avec PowerShell :
		```powershell
		New-ADUser -Name "Chris" -SamAccountName "User" -UserPrincipalName "user@domaine.lan" -AccountPassword (Read-Host -AsSecureString "Input Password") -Enabled $true
		```
		
	- Redirection du conteneur de création par défaut :
		- Par défaut, les nouveaux utilisateurs sont créés dans le conteneur `CN=Users`.
		- Commande pour rediriger la création par défaut vers une OU dédiée :
			```cmd
			redirusr OU=DefaultOU,DC=domaine,DC=lan
			```
			
	- **Astuce** : Pour récupérer le `distinguishedName` exact de l'OU cible, ouvrir la console **Utilisateurs et ordinateurs Active Directory**, activer les **Fonctionnalités avancées** (menu Affichage), puis consulter l'onglet **Éditeur d'attributs** dans les propriétés de l'OU.

3. **Jonction d'un poste client**
	- **Jonction standard au domaine** :
		- **Prérequis :** Édition Windows Professionnel ou Entreprise requise.
		- Définir l'adresse IPv4 du contrôleur de domaine comme serveur DNS principal sur la carte réseau du poste client.
		- Accéder aux *Propriétés système* du client (clic droit sur *Ce PC* > *Modifier les paramètres* ou via `Renommer ce PC (avancé)`).
		- Cocher *Domaine*, saisir le nom complet (`domaine.lan`) et renseigner les identifiants d'un compte autorisé.
		- Redémarrer le poste client.
		- Connexion utilisateur via `Utilisateur@domaine.lan` ou `DOMAINE\Utilisateur`.
		- Vérification : L'objet ordinateur est créé automatiquement dans le conteneur `CN=Computers`.

4. **Réinitialisation d'un poste client**
	- **Réinitialisation du compte d'ordinateur** :
		- **Problème** : Une restauration système du poste client désynchronise le mot de passe de la machine et détruit la relation de confiance sécurisée établie avec l'AD.
		- **Avantage** : La réinitialisation du compte (plutôt que sa suppression) préserve son emplacement hiérarchique (OU) et ses autorisations associées.
		- **Procédure serveur** : Dans **Utilisateurs et ordinateurs Active Directory**, clic droit sur l'objet ordinateur > *Réinitialiser le compte*.
		- Procédure client :
			- Ouvrir une session administrateur local en utilisant le préfixe `.\` comme nom d'utilisateur.
			- Sortir la machine du domaine en la basculant dans un *Groupe de travail* temporaire, puis redémarrer.
			- Rejoindre de nouveau le domaine Active Directory avec un compte autorisé, puis redémarrer.

---
### Gestion et Stratégie d'Implantation des Groupes

1. **Typologie et caractéristiques des groupes**
	- **Groupes de distribution** :
		- Utilisés exclusivement pour les listes de diffusion de messagerie (ex: Microsoft Exchange).
		- **Restriction :** Dépourvus d'attribut `objectSID` ; utilisables sous aucun prétexte pour attribuer des autorisations NTFS ou des partages réseau.
		
	- **Groupes de sécurité** :
		- Possèdent un `objectSID`.
		- Servent à définir les listes de contrôle d'accès (ACL / autorisations NTFS et de partage) et peuvent également être utilisés comme listes de diffusion.
		
	- **Exception :** La conversion directe entre un type Sécurité et Distribution est autorisée si le niveau fonctionnel du domaine est au minimum Windows Server 2000 natif.

2. **Scopes - Étendues de groupe**
	- **Domaine local** :
		- **Membres acceptés** : Comptes utilisateurs, comptes d'ordinateurs, groupes globaux de tout domaine de la forêt, et groupes universels de toute la forêt.
		- **Portée des autorisations** : Strictement limitée aux ressources situées dans le domaine local où le groupe réside.
		
	- **Globale** :
		- **Membres acceptés** : Comptes et groupes globaux provenant uniquement du domaine d'appartenance du groupe.
		- **Portée des autorisations** : Utilisable pour sécuriser des ressources réparties sur l'ensemble des domaines approuvés de la forêt.
		
	- **Universelle** :
		- **Membres acceptés** : Comptes, groupes globaux et autres groupes universels provenant de n'importe quel domaine de la forêt.
		- **Portée des autorisations** : Utilisable pour sécuriser des ressources sur l'ensemble de la forêt.
		- **Spécificité** : Ses membres sont répertoriés dans le **Catalogue Global (GC)**.
		
	- **Modification d'étendue** :
		- Le passage direct entre certaines étendues (ex: Globale vers Domaine local) est restreint par l'interface.
		- **Astuce :** Pour convertir une étendue bloquée, basculer temporairement le groupe en étendue **Universelle**, cliquer sur *Appliquer*, puis sélectionner l'étendue finale désirée avant de valider.

3. **Bonnes pratiques de structuration - Méthode AGDLP**
	- **Astuce :** Appliquer rigoureusement la méthodologie standard **AGDLP** pour l'attribution des droits :
		- **A** (Accounts) : Comptes utilisateurs et machines.
		- **G** (Global groups) : Placés dans des Groupes Globaux représentant une fonction ou un métier (ex: `GG_Comptabilite`).
		- **DL** (Domain Local groups) : Les groupes globaux sont imbriqués dans des Groupes Domaine Local représentant une ressource et un niveau d'accès (ex: `DL_PartageCompta_Lecture`).
		- **P** (Permissions) : Les autorisations NTFS ou de partages réseau sont assignées exclusivement au groupe Domaine Local.
		
	- **Attention :** Éviter l'imbrication excessive de groupes afin de limiter les risques d'attribution de privilèges accidentels par héritage et de simplifier les audits d'accès.

4. **Catégories de groupes système**
	- **Groupes intégrés (`Builtin`)** : Stockés dans le conteneur `Builtin`, d'étendue Domaine Local stricte, dédiés aux délégations de gestion du système d'exploitation (ex: *Opérateurs de sauvegarde*, *Utilisateurs du Bureau à distance*).
	- **Groupes prédéfinis (`Users`)** : Stockés dans le conteneur `Users`, dotés d'étendues prédéfinies fixes pour les rôles de haute sécurité (ex: *Admins du domaine*, *Administrateurs de l'entreprise*, *Administrateurs du schéma*).
	- **Groupes spéciaux** : Entités de sécurité dynamiques gérées exclusivement par le système d'exploitation sans modification manuelle possible (ex: *Tout le monde*, *Utilisateurs authentifiés*).

---
### Délégation d'Administration et Contrôle d'Accès

1. **Assistant Délégation de contrôle**
	- Accessible via clic droit sur un domaine, un **conteneur** ou une **OU** dans **Utilisateurs et ordinateurs Active Directory**.
	- Permet d'assigner des **tâches courantes** à un **utilisateur** ou **groupe** (le *Principal*) :
		- Réinitialisation des mots de passe utilisateurs.
		- Création, suppression et gestion des comptes utilisateurs.
		- Gestion de l'appartenance à des groupes spécifiques.
		
	- **Tâche personnalisée** :
		- Permet de cibler des classes d'objets spécifiques (ex: uniquement les objets `OrganizationalUnit` et `User`).
		- Sélection granulaire des autorisations (création/suppression d'objets enfants, lecture/écriture de propriétés spécifiques).
		
	- **Restriction :** Par mesure de sécurité, la délégation de contrôle standard ne s'applique pas aux comptes à privilèges protégés par le mécanisme AdminSDHolder (ex: compte *Administrateur* natif).

2. Paramètres de **sécurité avancés** et **délégation manuelle**
	- **Prérequis :** Activer l'affichage des **Fonctionnalités avancées** dans le menu *Affichage* pour faire apparaître l'onglet *Sécurité*.
	- **Accès** : Clic droit sur l'objet > *Propriétés* > onglet *Sécurité* > bouton *Avancé*.
	- Paramétrage fin d'une entrée de sécurité :
		- Sélection du *Principal* (utilisateur ou groupe bénéficiaire).
		- **Définition du *Type*** : *Autoriser* ou *Refuser*.
		- **Portée d'application** : Choix de l'héritage (*Cet objet uniquement*, *Cet objet et tous les objets descendants*, *Objets descendants uniquement*).
		- **Définition des permissions** : Autorisations globales ou accès en lecture/écriture ciblé sur des attributs individuels.
		- Toute permission personnalisée apparaît avec la désignation *Spéciale* dans la liste principale de l'onglet *Sécurité*.

3. **Délégation rapide** via l'**onglet "Géré par"**
	- Présent dans les **propriétés des groupes**, des RODC ou d'autres objets d'annuaire.
	- Pour un **groupe** : Permet d'assigner un gestionnaire et de cocher la case *Le gestionnaire peut mettre à jour la liste des membres* pour lui déléguer l'administration de l'appartenance sans lui donner de droits administratifs globaux.
	- Pour un **RODC** : Assigne le compte responsable de l'administration locale du contrôleur en lecture seule.

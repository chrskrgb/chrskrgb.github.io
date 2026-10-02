---
tags:
  - Note
  - ADDS
  - Admin
  - IT
  - Système
  - Microsoft
---
## Groupe principal et clients macOS dans Active Directory

1. **Concept et fonctionnement par défaut**
	- **Définition :** L'attribut `primaryGroupID` définit le groupe principal d'un utilisateur dans **Active Directory**, attribué par défaut à **Utilisateurs du domaine** (`Domain Users`).
	- **Origine :** Mécanisme hérité des systèmes **POSIX / Unix / Linux** nécessitant un identifiant de groupe unique (`GID`) pour chaque utilisateur et fichier.
	- **Recommandation :** Ne pas modifier ce réglage par défaut dans 99 % des cas pour éviter de perturber les applications Windows standards.

2. **Cas de modification légitimes**
	- **Restriction de comptes sensibles :** Permet d'isoler un compte (service, invité, externe) en changeant son groupe principal pour pouvoir ensuite le retirer du groupe **Utilisateurs du domaine** et supprimer ses droits implicites.
	- **Interopérabilité applicative :** Requis pour les anciennes bases de données, les progiciels `Legacy` ou le stockage réseau **NFS** qui interrogent cet attribut pour valider les droits.
	- **Intégration de clients macOS :** Nécessaire pour aligner la gestion des permissions de fichiers de type **UNIX** avec l'infrastructure Windows.

3. **Application concrète clients macOS**
- **Scénario de partage de fichiers mixte (Windows/Mac) :**
	- **Problème :** Par défaut, un fichier créé depuis un Mac prend l'étiquette du groupe principal de l'auteur (`Utilisateurs du domaine`), ce qui expose les données confidentielles à toute l'entreprise et provoque des conflits de droits lors des blocages manuels.
	- **Résolution :** Assigner un groupe AD ciblé (ex: `Studio`) comme groupe principal pour restreindre automatiquement l'accès aux seuls membres concernés.
- **Scénario d'homogénéisation des permissions locales :**
	- **Problème :** Les scripts et outils locaux du Mac (ex: `Docker`, privilèges administrateurs) attendent un `GID` maîtrisé ou local (comme `staff`), rejetant les identifiants trop longs ou dynamiques générés par le plugin AD standard.
	- **Résolution :** Configurer les attributs **UNIX** via la norme `RFC 2307` dans Active Directory pour imposer un `GID` fixe (ex: `1020`) reconnu immédiatement par le système macOS.

1. **Restrictions techniques**
	- **Type de groupe :** Le nouveau groupe affecté doit obligatoirement être un groupe de **Sécurité**.
	- **Portée du groupe :** Le groupe doit posséder une portée **Global** ou **Universel** ; les groupes de portée **Domaine Local** sont techniquement interdits.

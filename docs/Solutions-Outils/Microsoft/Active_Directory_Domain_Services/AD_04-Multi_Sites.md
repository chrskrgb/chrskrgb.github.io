---
tags:
  - Document
  - ADDS
  - Admin
  - IT
  - Système
  - Microsoft
---
## Déploiement, Topologie et Réplication Multi-Sites
---
---
### Déploiement des Contrôleurs de Domaine
1. **Déploiement du premier DC** (Création d'une nouvelle forêt)
	- **Configuration IP** : Assigner une adresse IPv4 statique au serveur, désactiver le protocole TCP/IPv6, puis désactiver/réactiver la carte réseau.
	- **Installation du rôle** : Dans le *Gestionnaire de serveur*, lancer l'assistant *Ajouter des rôles et des fonctionnalités* et cocher **Services AD DS** (le service serveur DNS est sélectionné automatiquement).
	- **Promotion du serveur** :
		- Lancer l'assistant de promotion post-installation.
		- Sélectionner l'opération *Ajouter une nouvelle forêt* et spécifier le nom DNS racine (ex: `domaine.lan`).
		- Définir le niveau fonctionnel de la forêt et du domaine (recommander la version la plus récente).
		- Vérifier que les options **Serveur DNS** et **Catalogue global (GC)** sont cochées.
		- **Restriction :** L'option *Contrôleur de domaine en lecture seule (RODC)* est rigoureusement impossible sur le premier DC d'une forêt.
		- Définir le mot de passe de restauration des services d'annuaire (**DSRM**).
		- Ignorer l'avertissement relatif à la délégation DNS (normal en l'absence de zone parente faisant autorité).
		- Confirmer le nom NetBIOS du domaine généré (ex: `DOMAINE`).
		- Conserver les chemins système par défaut : base de données NTDS (`C:\Windows\NTDS`) et partage SYSVOL (`C:\Windows\SYSVOL`).
		- Valider les prérequis, lancer l'installation et laisser le serveur redémarrer automatiquement.
		- Connexion post-redémarrage avec le compte administrateur local, désormais promu administrateur du domaine.
		
	- Automatisation PowerShell pour la création de forêt :
		```powershell
		Import-Module ADDSDeployment
		Install-ADDSForest `
		-CreateDnsDelegation:$false `
		-DatabasePath "C:\Windows\NTDS" `
		-DomainMode "WinThreshold" `
		-DomainName "domaine.lan" `
		-DomainNetbiosName "domaine" `
		-ForestMode "WinThreshold" `
		-InstallDns:$true `
		-LogPath "C:\Windows\NTDS" `
		-NoRebootOnCompletion:$false `
		-SysvolPath "C:\Windows\SYSVOL" `
		-Force:$true
		```

2. **Ajout d'un contrôleur de domaine** (réplica à un domaine existant)
	- **Objectif** : Assurer la haute disponibilité (minimum 2 contrôleurs par domaine) et réduire la latence réseau des utilisateurs distants.
	- **Préparation** : Configurer la carte réseau du nouveau serveur (DC2) en renseignant l'adresse IP du premier contrôleur (DC1) comme serveur DNS principal.
	- **Installation et promotion** :
		- Installer le rôle **Services AD DS** via le *Gestionnaire de serveur*.
		- Lancer la promotion et choisir *Ajouter un contrôleur de domaine à un domaine existant*.
		- Renseigner les identifiants d'un compte administrateur du domaine sous la forme `DOMAINE\Administrateur` ou `admin@domaine.lan`.
		- Cocher **Serveur DNS** et **Catalogue global (GC)** (laisser l'option RODC décochée).
		- Sélectionner le site Active Directory d'appartenance.
		- Ignorer l'avertissement sur la délégation DNS.
		- Source de réplication : Sélectionner explicitement le premier DC existant pour initialiser le transfert de données.
		- Conserver les chemins de stockage par défaut, finaliser l'installation et laisser le serveur redémarrer.
		
	- **Impératif :** Une fois le second contrôleur opérationnel, inscrire son adresse IP en tant que serveur DNS secondaire sur l'ensemble des configurations IP des machines clientes pour assurer la continuité de service en cas de panne de DC1.

3. **Déploiement domaine enfant** (Sous-domaine)
	- **Règle** : Un contrôleur de domaine ne peut gérer qu'un seul domaine ou sous-domaine. La création d'un domaine enfant exige le déploiement d'un nouveau serveur dédié.
	- Installation et configuration :
		- Installer le rôle **Services AD DS** sur le serveur dédié.
		- Dans l'assistant de promotion, sélectionner *Ajouter un nouveau domaine à une forêt existante*.
		- Renseigner les identifiants d'administration du domaine parent.
		- Type de domaine : Sélectionner *Domaine enfant* et spécifier son nom court (ex: `web` pour obtenir `web.domaine.lan`).
		- Pour un domaine racine totalement distinct au sein de la même forêt, sélectionner *Domaine d'arborescence*.
		- Les niveaux fonctionnels du sous-domaine peuvent être configurés à un niveau inférieur à celui de la forêt si la prise en charge d'anciens contrôleurs l'exige.
		- Valider le nom NetBIOS généré automatiquement (ex: `WEB`).
		
	- **Relations de confiance** et **délégation DNS** :
		- Approbation automatique : Une relation de confiance bidirectionnelle et transitive est créée de manière native entre le domaine parent et le domaine enfant.
		- Délégation DNS automatique : La zone DNS du sous-domaine est hébergée sur le nouveau contrôleur. Sur le serveur DNS du domaine parent, un dossier de délégation apparaît automatiquement, renvoyant l'ensemble des requêtes vers le serveur DNS du domaine enfant.

4. **IFM - Install From Media** (Déploiement à partir d'un support physique)
	- **Objectif** : Éviter la saturation de la bande passante WAN lors du déploiement d'un contrôleur sur un site distant en réalisant la synchronisation initiale de la base de données via un support physique amovible (disque dur, clé USB).
	- **Restriction :** Le serveur source utilisé pour générer le support IFM doit impérativement être un contrôleur en lecture/écriture (un RODC ne peut pas servir de source IFM).
	- Génération du support IFM sur le DC source via `ntdsutil` :
		```cmd
		ntdsutil
		Activate instance NTDS
		ifm
		Create SYSVOL full E:
		quit
		quit
		```
		
	- **Structure générée sur le support de stockage amovible (`E:`)** :
		- Dossier `Active Directory` : Contient `ntds.dit` (base de données AD) et `ntds.jfm` (fichier de protection des transactions).
		- Dossier `registry` : Contient les ruches système du registre Windows `SECURITY` et `SYSTEM`.
		- Dossier `SYSVOL` : Contient l'ensemble des stratégies (`Policies`) et des scripts de domaine.
		
	- **Installation du contrôleur distant** :
		- Installer le rôle **Services AD DS** sur le serveur de destination.
		- Dans l'assistant de promotion (*Ajouter un contrôleur de domaine à un domaine existant*), cocher l'option **Installation à partir du support** et spécifier le chemin local du support USB.
		- Sélectionner le contrôleur source pour initier la réplication réseau : celle-ci transférera uniquement le différentiel généré depuis la création du support IFM.

5. **Déploiement et sécurisation d'un RODC**
	- **Installation** :
		- Déployer le rôle **Services AD DS** et lancer la promotion vers un domaine existant.
		- Cocher obligatoirement la case **Contrôleur de domaine en lecture seule (RODC)**.
	- **Options et restrictions** :
		- **Restriction :** Aucune opération d'écriture directe n'est permise sur un RODC (les options de création, modification ou suppression d'objets sont grisées dans les consoles).
		- Compte d'administrateur délégué : Possibilité de désigner un utilisateur standard comme gestionnaire local du serveur (paramétrable dans l'assistant ou via l'onglet *Géré par* de l'objet ordinateur du RODC).
			- **Astuce :** Ajouter le compte d'administrateur délégué au *Groupe de réplication dont le mot de passe RODC est autorisé* pour lui permettre d'ouvrir une session et d'administrer le RODC même si la liaison WAN avec le siège est coupée.
			- **Droits restreints** : L'administrateur délégué ne peut modifier que les utilisateurs appartenant au groupe de réplication autorisé.
	- **PRP - Password Replication Policy** (Stratégies de réplication des mots de passe) :
		- *Groupe de réplication dont le mot de passe RODC est autorisé* : Comptes autorisés à mettre leur mot de passe en cache local.
		- *Groupe de réplication dont le mot de passe RODC est refusé* : Bloque expressément la mise en cache locale des comptes sensibles et administratifs (membres des groupes Admins du domaine, Administrateurs de l'entreprise, etc.).
		- Gestion avancée (onglet *Stratégie de réplication de mot de passe*) :
			- Visualiser les comptes dont le mot de passe est stocké localement (inclut le compte technique propre au contrôleur `krbtgt_*****`).
			- Afficher la liste des comptes récemment authentifiés par le serveur.
			- Utiliser l'option *Préremplir les mots de passe* pour forcer la mise en cache préventive et accélérer les futures connexions d'utilisateurs distants.
			- Utiliser l'onglet *Stratégie résultante* pour vérifier les droits finaux de réplication d'un utilisateur cible.

---
### Évolution des Niveaux Fonctionnels

1. Principes d'élévation
	- Verrouille l'activation de fonctionnalités avancées pour préserver la rétrocompatibilité avec les versions antérieures de Windows Server.
	- **Règle** : Le niveau fonctionnel du domaine ne peut excéder la version d'OS du DC le plus ancien du domaine. Le niveau de la forêt ne peut excéder le niveau du domaine le plus bas de la forêt.
	- **Séquencement impératif** : Élever l'ensemble des niveaux fonctionnels de domaine avant de procéder à l'élévation du niveau de la forêt.
	- **Restriction** : Toute élévation de niveau fonctionnel réalisée depuis les consoles graphiques est irréversible.

2. Chronologique des fonctionnalités
	- **Windows Server 2003** :
		- **Niveau forêt** : Relations d'approbation de forêt (fusions d'entreprises) ; renommage et déplacement de domaines ; réplication des valeurs liées (LVR - réplication granulaire des membres de groupes) ; prise en charge du déploiement des RODC.
		- **Niveau domaine** : Utilitaire en ligne de commande `netdom.exe` ; réplication de l'attribut `lastLogonTimestamp` ; redirection personnalisée des conteneurs de création par défaut (`redirusr`, `redircmp`) ; délégations de contrôle avancées ; authentification sélective.
		
	- **Windows Server 2008** :
		- **Niveau domaine** : Migration et utilisation de la réplication **DFSR** pour le dossier SYSVOL (abandon définitif de FRS) ; chiffrement AES 128 et AES 256 bits pour le protocole Kerberos ; bureaux virtuels personnels.
		
	- **Windows Server 2008 R2** :
		- **Niveau forêt** : Intégration de la **Corbeille Active Directory** (restauration d'objets supprimés sans perte de liaisons).
		- **Niveau domaine** : Détection fine du type d'ouverture de session (identifiants standards ou carte à puce).
		
	- **Windows Server 2012** :
		- **Niveau domaine** : Prise en charge des revendications d'autorisation KDC, du contrôle d'accès dynamique (DAC) et des modèles d'administration centralisés.
		
	- **Windows Server 2012 R2** :
		- **Niveau domaine** : Groupe de sécurité global *Utilisateurs protégés* ; mise en œuvre des stratégies d'authentification et des silos de stratégies d'authentification.
		
	- **Windows Server 2016** :
		- **Niveau forêt** : Gestion des accès privilégiés (**PAM** - Privileged Access Management) avec appartenances temporaires à des groupes à privilèges.
		- **Niveau domaine** : Mises à jour de sécurité et durcissement du protocole NTLM ; actualisation des SID d'identité de clé publique pour les clients Kerberos exploitant PKInit.

---
### Topologie Physique et Réplication Multi-Sites

1. **Concepts et composants du partitionnement physique**
	- **Sites Active Directory** : Délimitations géographiques de l'infrastructure réseau basées sur des sous-réseaux IP haut débit, configurées dans la console **Sites et services Active Directory**.
	
	- **Objets de connexion** :
		- Liens logiques unidirectionnels reliant deux contrôleurs de domaine partenaires pour orchestrer la réplication.
		- Associés à l'objet système *Paramètres NTDS* de chaque serveur.
		- Générés automatiquement par le service KCC.
		- **Restriction :** La modification ou création manuelle d'un objet de connexion désactive définitivement la prise en charge dynamique de cet objet spécifique par le KCC.
		
	- **KCC - Knowledge Consistency Checker**
		- Processus d'arrière-plan s'exécutant sur chaque DC pour calculer dynamiquement la topologie de réplication optimale (anneau de réplication).
		- Réajuste la topologie lors de l'ajout, du déplacement ou de la suppression de contrôleurs.
		- Génère des routes alternatives temporaires en cas d'indisponibilité d'un serveur.
		- Un unique service KCC prend en charge le calcul des connexions inter-sites.
		
	- **Bridgehead Server** (Tête de pont) : Contrôleur de domaine désigné au sein d'un site pour centraliser et transmettre les flux de réplication inter-sites vers les autres sites géographiques.

2. **Réplication intrasite vs Intersite**
	- **Réplication intrasite** (site local)
		- Optimisée pour les liaisons LAN à haut débit et faible latence.
		- Déclenchement sur notification : Lorsqu'une modification survient sur un DC, celui-ci avertit son premier partenaire après un délai de 15 secondes, puis espace les notifications suivantes de 30 secondes pour chaque partenaire additionnel.
		- Utilise le protocole RPC over IP non compressé pour préserver les ressources CPU.
		- Scrutation de cohérence passive programmée toutes les heures.
		- **Exception :** Les opérations de sécurité critiques (verrouillage de compte, réinitialisation de mot de passe) déclenchent une réplication urgente immédiate, sans aucun délai de notification.
		
	- **Réplication intersite** (sites distants)
		- Planifiée par défaut toutes les 15 minutes afin d'éviter l'engorgement de la bande passante.
		- Repose sur la configuration de liens de sites IP (`Inter-Site Transports > IP`) pondérés par un paramètre numérique de coût et une plage horaire autorisée.
		- Utilise par défaut le protocole RPC over IP compressé (environ 15 % de la taille initiale des données).
		- **Exception :** Prise en charge possible du protocole asynchrone SMTP sur des liaisons extrêmement instables, avec une **restriction majeure** : SMTP est incapable de répliquer la partition de domaine et le partage de fichiers SYSVOL.

3.  **Infrastructure multi-sites**
	- **Interconnexion réseau VPN** (Rôle RRAS)
		- Configurer deux passerelles VPN (ex: `site_a-vpn` et `site_b-vpn`).
		- Déclarer des comptes de liaison locaux (`user_a`, `user_b`) avec mot de passe n'expirant jamais et accès accordé dans *Appel entrant*.
		- Installer le rôle *Accès à distance* avec les fonctionnalités *VPN* et *Routage*.
		- Configurer un tunnel VPN L2TP de site à site avec routage d'itinéraires statiques et NAT.
		- **Configuration DNS des passerelles VPN :** Supprimer tout serveur DNS de l'interface WAN ; configurer l'interface LAN pour pointer vers les DC locaux (ex: `10.0.1.11` et `10.0.1.12` sur Site A ; `10.0.2.11` et `10.0.2.12` sur Site B).
		
	- **Déploiement des contrôleurs de domaine**
		- **Site A** : Installer la forêt sur `site_a-dc1` (DNS : `127.0.0.1` en préféré, `10.0.1.12` en auxiliaire), puis joindre le réplica `site_a-dc2` (DNS : `127.0.0.1` en préféré, `10.0.1.11` en auxiliaire).
		- **Site B** : Définir l'adresse de `site_a-dc1` en DNS préféré sur `site_b-dc1`, promouvoir les serveurs `site_b-dc1` et `site_b-dc2`, puis réajuster leurs DNS locaux (`127.0.0.1` et l'IP du DC partenaire local).
		
	- **Déclaration de la topologie** (Sites et services Active Directory)
		- Créer les objets sites : `Site A` et `Site B`.
		- Déclarer les sous-réseaux dans le dossier `Subnets` : associer `10.0.1.0/24` au Site A et `10.0.2.0/24` au Site B.
		- Dans `Inter-Site Transports > IP` : Créer un lien manuel de site liant le Site A et le Site B, définir son coût et sa planification horaire, puis supprimer le lien générique par défaut `DEFAULTIPSITELINK`.
		- Déplacer manuellement chaque serveur dans son site respectif, puis supprimer le conteneur vide initial `Default-First-Site-Name`.
		
	- **Migration des comptes de liaison VPN** vers l'annuaire Active Directory :
		- Créer les comptes `VpnUserSiteB` (sur Site A) et `VpnUserSiteA` (sur Site B) dans l'annuaire, autoriser l'accès dans *Appel entrant*, et propager immédiatement les comptes via réplication manuelle.
		- Joindre les serveurs VPN au domaine.
		- **Impératif :** Ne pas redémarrer immédiatement les serveurs VPN après la jonction : forcer d'abord la réplication AD globale pour distribuer les nouveaux comptes d'ordinateurs, et redémarrer uniquement après validation.
		- Reconfigurer les interfaces de routage à la demande avec les identifiants AD (`DOMAINE\VpnUser...`), reconnecter les tunnels et activer les règles de pare-feu ICMP (ping).
		
	- **Autorisation des serveurs DHCP** d'entreprise :
		- Dans la console DHCP, autoriser les serveurs DHCP de chaque site.
		- Contrôler l'apparition des objets dans *Sites et services Active Directory* sous le conteneur `NetServices`.
		- Forcer la réplication de l'objet racine `DhcpRoot` sur l'ensemble de la forêt et redémarrer le service DHCP pour activer la distribution des baux IPv4.

4. **Commandes de diagnostic** et **réplication**
	- Forcer le recalcul immédiat de la topologie par le KCC :
		```cmd
		repadmin /kcc
		```

	- Forcer la synchronisation manuelle exhaustive de toutes les partitions d'annuaire :
		```cmd
		repadmin /syncall /A /e /P
		```
		
	- Paramètres de `repadmin /syncall` :
		- `/A` : Synchronise toutes les partitions du serveur.
		- `/e` : Traverse l'ensemble des sites de l'infrastructure (réplication inter-sites).
		- `/P` : Pousse (push) les modifications vers les contrôleurs partenaires.
		- `/D` : Identifie les serveurs par leur nom distinctif dans les messages.

	- Forcer la réplication Active Directory en PowerShell :
		```powershell
		Sync-ADReplication
		repadmin /syncall /AeD
		```

	- Forcer l'interrogation de l'annuaire et la réplication du moteur DFSR pour le partage SYSVOL :
		```cmd
		dfsrdiag PollAD
		```

	- Contrôler l'état de santé opérationnel du moteur DFSR :
		```cmd
		dfsrdiag replicationstate
		```

	- Script de vérification à distance de la présence du Magasin Central sur l'ensemble des DC :
		```powershell
		Get-ADDomainController -Filter * | Select-Object Name | ForEach-Object { 
		    $path = "\\$($_.Name)\SYSVOL\krgb.lan\Policies\PolicyDefinitions"
		    Write-Host "$($_.Name) : $(Test-Path $path)" 
		}
		```

---
tags:
  - Document
  - ADDS
  - Admin
  - IT
  - Système
  - Microsoft
---
## Stratégies de Groupe (GPO) et Préférences (GPP)

---
---
### Architecture, Hiérarchie et Cycle d'Application des GPO

1. **Rôle et capacités fondamentales**
	- Centralisation de la configuration et des paramètres de sécurité des parcs de serveurs, PC de bureau et environnements virtuels (VDI).
	- Déploiement automatisé d'applications logicielles packagées au format Windows Installer (`.msi`).
	- Gestion centralisée des profils itinérants et de la redirection des dossiers utilisateurs.
	- Contrôle standardisé des composants système de l'OS (Microsoft Defender, Windows Update, paramètres du Bureau à distance).

2. **Règle LSDOU** (Hiérarchie d'évaluation et ordre de priorité)
	- L'évaluation des stratégies s'exécute dans l'ordre strict suivant (le dernier appliqué écrase les valeurs précédentes en cas de conflit) :
		1. **L**ocal : Stratégie de groupe locale de la machine physique (`gpedit.msc`).
		2. **S**ite : Stratégies liées au site physique Active Directory d'appartenance.
		3. **D**omaine : Stratégies liées à la racine du domaine Active Directory.
		4. **OU** (Unité d'Organisation) : Stratégies liées aux unités d'organisation parentes, puis descendantes.
		
	- **OU imbriquées** : En cas de conflit, les paramètres appliqués à une OU enfant écrasent ceux définis sur l'OU parente supérieure.
	- Mécanismes de **dérogation à la règle LSDOU** :
		- **Bloquer l'héritage :** Configuré sur une OU pour empêcher l'application descendante des GPO provenant des niveaux hiérarchiques supérieurs (domaines ou OU parentes).
		- **Option "Appliqué" (Enforced) :**
			- Activée sur un lien de GPO pour forcer son exécution sans exception sur l'ensemble de l'arborescence descendante.
			- **Exception :** L'option *Appliqué* outrepasse systématiquement tout blocage de l'héritage configuré sur une OU enfant et rend la GPO supérieure prioritaire.

3. **Cycle de rafraîchissement des stratégies**
	- **Configuration Ordinateur** : Appliquée au démarrage du système d'exploitation et lors des cycles périodiques d'actualisation.
	- **Configuration Utilisateur** : Appliquée à l'ouverture de session de l'utilisateur et lors des cycles périodiques.
	- **Périodicité automatique** :
		- **Postes clients et serveurs membres** : Rafraîchissement automatique toutes les 90 minutes, pondéré par un décalage aléatoire de 0 à 30 minutes (pour lisser la charge réseau).
		- **Contrôleurs de domaine** : Rafraîchissement automatique toutes les 5 minutes.
		
	- **Forçage manuel** de l'application :
		- En invite de commandes : `gpupdate /force`
		- En PowerShell : `Invoke-GPUpdate`

4. **Règles de liaison et filtrage**
	- **Règle** : Un objet GPO peut être lié exclusivement à un Site, un Domaine ou une Unité d'Organisation (OU). Il est techniquement impossible de lier une GPO à un conteneur générique par défaut (`CN=Computers`, `CN=Users`).
	- **Deux GPO par défaut** générées à l'installation :
		- ***Default Domain Policy*** : Liée à la racine du domaine (définit les stratégies de mots de passe, de verrouillage de compte et de sécurité globale).
		- ***Default Domain Controllers Policy*** : Liée à l'OU des contrôleurs de domaine (politiques de sécurité spécifiques aux serveurs d'annuaire).
		
	- **Filtrage de sécurité** :
		- Par défaut, toute GPO est configurée avec le groupe *Utilisateurs authentifiés* (contenant l'ensemble des utilisateurs et ordinateurs du domaine).
		- Pour restreindre l'application d'une GPO : Supprimer *Utilisateurs authentifiés* des autorisations d'application et ajouter le groupe de sécurité spécifique désiré.
		
	- **Filtrage WMI** (Windows Management Instrumentation) :
		- Permet de conditionner l'exécution d'une GPO selon des critères matériels ou logiciels précis du client (version de l'OS, architecture 32/64 bits, espace disque disponible).
		- **Préparation** : Toujours tester la syntaxe WQL sous l'espace de noms `root\CIMv2` (via les outils `wbemtest.exe`, *Microsoft WMI Code Creator*, ou la commande `winver.exe`).

5. Traitements avancés : **Bouclage et liaisons lentes**
	- Traitement par bouclage de la stratégie de groupe (Loopback Processing) :
		- **Emplacement** : `Configuration ordinateur > Stratégies > Modèles d'administration > Système > Stratégie de groupe > Configurer le mode de traitement par bouclage de la stratégie de groupe utilisateur`.
		- **Rôle** : Appliquer les paramètres de la section *Configuration Utilisateur* d'une GPO à toute personne ouvrant une session sur un ordinateur ciblé (indispensable pour les serveurs RDS et les salles en libre-service).
		- Modes de **fonctionnement** :
			- Mode **Remplacer** : La liste des GPO utilisateur appliquée est totalement substituée par les paramètres définis sur l'ordinateur (les GPO utilisateur habituelles sont ignorées).
			- Mode **Fusionner** : Les GPO utilisateur de la machine s'ajoutent à la liste des GPO de l'utilisateur. En cas de conflit direct sur un paramètre, la valeur issue de la GPO de l'ordinateur est prioritaire.
			
	- **Slow-link detection** (Gestion des liaisons lentes) :
		- **Impact** : En cas de détection d'une liaison réseau lente, l'OS désactive l'application des GPP, le déploiement de logiciels et la redirection de dossiers pour ne conserver que les stratégies de sécurité et les modèles d'administration.
		- Seuil de détection par défaut : Débit inférieur à 500 Kbits/s.
		- **Emplacement** : `Configuration ordinateur (ou utilisateur) > Stratégies > Modèles d'administration > Système > Stratégie de groupe > Configurer la détection d'une liaison lente de stratégie de groupe`.
		- **Paramètres** : Définir le seuil en Kbits/s, désactiver la détection en assignant la valeur `0`, ou cocher *Toujours traiter les connexions WWAN comme une liaison lente* (spécificité ordinateur).

---
### GPO Starter (Modèles de Stratégies)

1. **Concept et utilité**
	- Un GPO Starter constitue un patron de départ préconfiguré permettant d'instancier de nouvelles GPO classiques sans redéfinir manuellement les paramètres récurrents.
	- **Restriction** : Seule la section **Modèles d'administration** (`Administrative Templates` pour l'ordinateur et l'utilisateur) est supportée au sein d'un GPO Starter.
	- **Règle de déploiement** : Un GPO Starter ne peut jamais être lié directement à une cible (OU, domaine) : il sert exclusivement de modèle source lors de la création d'un objet GPO conventionnel.

2. **Fichiers CAB** (Gestion et portabilité)
	- **Initialisation** : Dans la console *Gestion de stratégie de groupe*, sélectionner le conteneur *Objets GPO Starter* et cliquer sur *Créer le dossier des objets GPO Starter* (génère les modèles par défaut).
	- **Exportation** : Un GPO Starter peut être exporté sous forme d'archive compressée au format `.cab` pour être transféré vers un autre domaine ou une forêt distante.
	- **Structure** interne du fichier **CAB exporté** :
		- `Machine_Comment.cmtx` : Fichier XML contenant les commentaires de stratégie.
		- `Machine_Registry.pol` : Fichier binaire compilant les modifications du registre configurées.
		- `Report.html` : Rapport visuel exhaustif documentant l'ensemble des stratégies activées et leurs valeurs.
		- `StarterGPO.tmplx` : Fichier de métadonnées XML structurant l'objet GPO Starter.

---
### Modèles d'Administration (ADM / ADMX) et Magasin Central

1. **ADM** vs **ADMX / ADML**
	- Format **ADM** (Hérité) : Fichier monolithique combinant les définitions de clés et les chaînes de texte de traduction, provoquant des corruptions d'affichage et l'inflation de l'espace SYSVOL.
	- Format **ADMX** (Moderne) : Architecture scindée basée sur le standard XML :
		- Fichiers `.admx` : Définissent la structure technique des paramètres et l'arborescence des clés de registre ciblées.
		- Fichiers `.adml` : Contiennent les traductions linguistiques associées, stockés dans des sous-dossiers spécifiques à chaque langue (ex: `fr-FR`, `en-US`).
	- Prise en charge des modèles tiers : Permet d'intégrer l'administration de solutions tierces (Google Chrome, Microsoft Office, Mozilla Firefox, Citrix, VMware).

2. Procédure de déploiement du Magasin Central (Central Store)
	- Emplacement local d'origine : `C:\Windows\PolicyDefinitions`.
	- **Règle**  : Le dossier central hébergé sur SYSVOL doit obligatoirement et strictement être nommé `PolicyDefinitions`.
	- Déploiement en invite de commandes administrateur :
		```cmd
		mkdir C:\Windows\SYSVOL\sysvol\domaine.lan\Policies\PolicyDefinitions
		xcopy C:\Windows\PolicyDefinitions\* C:\Windows\SYSVOL\sysvol\domaine.lan\Policies\PolicyDefinitions\ /E /H /C /I /Y
		```
		
	- Méthode alternative via l'Explorateur Windows :
		- Ouvrir une invite de commandes en mode administrateur.
		- Relancer l'explorateur avec les privilèges élevés complets :
			```cmd
			taskkill /f /im explorer.exe && start explorer.exe
			```
			
		- Créer manuellement le dossier `PolicyDefinitions` sous `C:\Windows\SYSVOL\sysvol\domaine.lan\Policies\` et y coller l'arborescence des modèles.
		
	- **Impératif :** Ne jamais tenter d'altérer manuellement les permissions de sécurité NTFS sur le dossier de partage SYSVOL pour contourner les erreurs d'accès refusé liées au token administrateur filtré (UAC Split-Token).
	- Intégration de **modèles tiers** : Copier les fichiers `.admx` directement à la racine de `PolicyDefinitions` et les fichiers `.adml` dans les sous-dossiers linguistiques correspondants (ex: `fr-FR`).
	- **Vérification** : Ouvrir `gpmc.msc`, éditer une GPO et naviguer dans *Modèles d'administration* : la mention explicite *Définitions de stratégies (fichiers ADMX) récupérées à partir du magasin central* doit obligatoirement s'afficher à la racine du nœud.

2. **GPS - Group Policy Search** (Moteur de recherche)
	- **Contexte** : Windows ne propose aucun mécanisme d'indexation ou de recherche textuelle globale de paramètres dans l'éditeur de GPO.
	- **Solution** : Utiliser l'outil de référence en ligne *Group Policy Search (GPS)*.
	- **Fonctionnalités d'analyse** :
		- Localisation du chemin complet du paramètre dans l'arborescence de la console GPMC.
		- Indication de la cible (*Configuration Ordinateur* ou *Configuration Utilisateur*).
		- Référencement du nom du fichier `.admx` source et des versions de systèmes d'exploitation compatibles.
		- Identification précise des ruches, clés et valeurs du registre Windows modifiées (vue `Registry View`).
	- **Astuce :** Utiliser les options multilingues de GPS pour convertir instantanément l'intitulé d'une stratégie anglophone issue d'une documentation technique vers son libellé exact au sein de la console francophone.

---
### Préférences de Stratégie de Groupe (GPP) et Exécution de Scripts

1. **Comparatif : GPO vs GPP**
	- **Stratégie standard (GPO)** : Configuration imposée de manière stricte par le système, verrouillée automatiquement et non modifiable par l'utilisateur final.
	- **Préférence (GPP)** : Paramètre appliqué initialement par défaut, mais laissant l'utilisateur libre de modifier la valeur ultérieurement sans qu'elle soit écrasée (sauf reconfiguration explicite).

2. **Modes d'action des préférences (CRUD)**
	- **Créer** : Applique le paramètre uniquement s'il n'existe pas déjà sur le poste client.
	- **Remplacer** : Supprime intégralement l'élément existant sur le poste pour le recréer à neuf (rare).
	- **Mettre à jour** : Crée l'élément s'il est absent, ou modifie ses valeurs si l'élément existe déjà (mode standard le plus employé).
	- **Supprimer** : Retire impérativement l'élément ciblé du poste client.
		- **Impératif :** Pour supprimer une ressource déployée par GPP (lecteur, imprimante, raccourci), il est impératif d'utiliser l'action *Supprimer* : effacer simplement l'objet GPP dans la console serveur laissera la configuration active sur le poste client.

3. Périmètre d'action des **GPP Ordinateur et Utilisateur**
	- **Préférences Ordinateur** :
		- Variables d'environnement système (ex: `PATH`).
		- Création d'arborescences de dossiers ou copie de fichiers depuis des partages réseau.
		- Gestion des fichiers `.ini` et injection de clés de registre complètes.
		- Déploiement de raccourcis, sources ODBC, imprimantes réseau et tâches planifiées.
		- Gestion des comptes et groupes locaux, configuration d'interfaces VPN et services Windows.
		
	- **Préférences Utilisateur** :
		- Mappage automatique de lecteurs réseau selon les autorisations d'accès.
		- Paramètres régionaux, options de date et d'heure.
		- Configuration des raccourcis du menu Démarrer et personnalisation des applications clientes.

4. Mécanisme de **ciblage au niveau de l'élément** (Item-Level Targeting)
	- Situé sous l'onglet *Commun* > *Ciblage* de chaque paramètre GPP.
	- Permet de conditionner l'application d'une préférence à des critères contextuels ultra-fins sans multiplier les OU :
		- Type et version du système d'exploitation.
		- Présence d'une clé ou d'une valeur de registre spécifique sur la machine.
		- Appartenance de l'ordinateur ou de l'utilisateur à un groupe de sécurité Active Directory.
		- Adresse IP, sous-réseau, plage d'espace disque disponible ou requêtes matérielles WMI.
		- Combinaison logique des règles via les opérateurs booléens `ET`, `OU`, `Est`, `N'est pas`.

5. **Déploiement de scripts** de démarrage, arrêt, connexion et déconnexion
	- **Déclencheurs** machine et session :
		- Configuration machine : `Configuration ordinateur > Stratégies > Paramètres Windows > Scripts (Démarrage/Arrêt)`.
		- Configuration session : `Configuration utilisateur > Stratégies > Paramètres Windows > Scripts (Ouverture/Fermeture de session)`.
		
	- **Impératif :** Utiliser le bouton *Afficher les fichiers* de la console pour copier les scripts directement dans le sous-dossier guidé du partage SYSVOL : cela garantit la réplication automatique du script sur l'ensemble des contrôleurs de domaine.
	- Exécution **PowerShell** :
		- Gérée via l'onglet dédié *Scripts PowerShell*.
		- Permet de définir des paramètres d'exécution en ligne de commande.
		- Offre le choix d'exécuter les scripts PowerShell avant ou après les scripts batch conventionnels.

---
### Diagnostic et Dépannage de l'Application des GPO

1. Outils de **diagnostic côté serveur**
	- **Console GPMC** (Résultats de stratégie de groupe) :
		- Évalue l'état réel appliqué en interrogeant un ordinateur et un utilisateur s'y étant déjà connecté.
		- L'onglet *Résumé* identifie les erreurs d'application et la détection d'une éventuelle liaison lente.
		- L'onglet *Détails* liste exhaustivement les GPO appliquées et celles refusées avec la raison du rejet.
		- L'onglet *Événements* filtre les journaux d'erreurs d'application GPO de la machine cible.
		
	- Modélisation de stratégie de groupe (Console GPMC) :
		- Moteur de simulation théorique permettant de tester l'impact du déplacement d'un ordinateur ou d'un utilisateur sans impacter l'infrastructure de production.
		- Permet de simuler un site Active Directory, une liaison lente, un traitement en boucle (*Remplacer* ou *Fusionner*), ou l'appartenance à de nouveaux groupes de sécurité.
		- **Attention :** Le résultat étant purement calculé, une validation sur un poste physique de test demeure obligatoire.

2. Outils de **diagnostic côté client**
- Commande en ligne `gpresult` :
- Génération d'un rapport HTML complet :
	```cmd
	gpresult /H c:\GPReport.html
	```

- Génération d'un rapport texte dans la console :
	```cmd
	gpresult /R
	```

- Niveaux de verbosité en mode texte : `/R` (synthèse standard), `/V` (détaillé et verbeux), `/Z` (exhaustivité totale).
- **Astuce :** Si l'exportation texte (`gpresult /R > c:\GPReport.txt`) affiche des caractères accentués corrompus dans le Bloc-notes, ouvrir le fichier avec Notepad++ en forçant l'encodage **OEM 850**.
	- Console graphique de jeu de stratégies résultant (`rsop.msc`) :
		- **Prérequis :** Lancer impérativement la console `rsop.msc` avec élévation administrateur pour afficher la section *Configuration Ordinateur* (dans le cas contraire, seuls les paramètres de la *Configuration Utilisateur* s'affichent).
		- Permet d'explorer graphiquement l'arborescence des paramètres appliqués et identifie explicitement le nom de la GPO source responsable de chaque configuration.
		
	- Console de gestion MMC personnalisée :
		- Exécuter `mmc.exe` en mode administrateur.
		- Naviguer dans *Fichier* > *Ajouter/Supprimer un composant logiciel enfichable* et insérer **Jeu de stratégie résultant**.
		- Permet de basculer en mode de journalisation pour capturer et auditer les données RSoP d'un utilisateur distant sur un poste du parc.
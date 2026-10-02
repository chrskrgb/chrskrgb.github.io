## Requête WMI & Espace de noms
**WMI** (Windows Management Instrumentation) : Interroge le matériel, le logiciel et la configuration Windows en temps réel.
**Langage WQL** : Syntaxe similaire au SQL (`SELECT * FROM Classe WHERE Condition`).
**PowerShell** : S'exécute via `Get-CimInstance -ClassName [Classe]`.

1. Exemples de classes courantes
	* **`Win32_ComputerSystem`** : Modèle de la machine.
	* **`Win32_OperatingSystem`** : Version de Windows (utilisé pour les filtres GPO).
	* **`Win32_Service`** : État des services Windows.

2. L'espace de noms `root\cimv2`
	* **`root\cimv2`** : Espace de noms par défaut de WMI.
	* **Rôle** : Contient la majorité des classes de gestion standard de l'ordinateur.
	* **Autres espaces** : `root\SecurityCenter2` (antivirus/pare-feu), `root\default` (registre), `root\rsop` (résultats de GPO).

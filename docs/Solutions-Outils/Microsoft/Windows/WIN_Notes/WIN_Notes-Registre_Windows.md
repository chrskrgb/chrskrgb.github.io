## Les 5 Ruches Principales

Une **ruche** (*hive*) est une structure de dossiers du Registre Windows contenant clés, sous-clés et valeurs.

1. **HKEY_LOCAL_MACHINE (HKLM)**
	* **Type :** Physique (`C:\Windows\System32\config\`).
	* **Portée :** Globale (tout l'ordinateur).
	* **Contenu :** Matériel, pilotes, OS et logiciels tiers.
	* **Sécurité :** Droits administrateur requis pour modification.

2. **HKEY_USERS (HKU)**
	* **Type :** Physique (Fichiers utilisateurs).
	* **Portée :** Globale.
	* **Contenu :** Base de données de tous les profils de la machine (via leur SID).
	* **Particularité :** Contient la clé `.DEFAULT` pour l'écran de verrouillage d'avant-session.

3. **HKEY_CURRENT_USER (HKCU)**
	* **Type :** Virtuel (Pointe vers le SID de l'utilisateur actif dans HKU).
	* **Portée :** Individuelle (session ouverte).
	* **Contenu :** Préférences personnelles, apparence, mode sombre et applications de l'utilisateur.
	* **Sécurité :** Modifiable par un utilisateur standard sans droits spécifiques.

4. **HKEY_CLASSES_ROOT (HKCR)**
	* **Type :** Virtuel (Fusion en RAM de HKLM\Software\Classes et HKCU\Software\Classes).
	* **Portée :** Mixte.
	* **Contenu :** Associations d'extensions de fichiers (`.txt`, `.pdf`) et composants COM.

5. **HKEY_CURRENT_CONFIG (HKCC)**
	* **Type :** Virtuel (Pointe vers HKLM\SYSTEM\CurrentControlSet\Hardware Profiles\Current).
	* **Portée :** Session matérielle en cours.
	* **Contenu :** Profil matériel actif au démarrage (affichage, imprimantes).

| Ruche    | Type     | Portée       | Contenu principal                                              | Sécurité / Droits     |
| :------- | :------- | :----------- | :------------------------------------------------------------- | :-------------------- |
| **HKLM** | Physique | Globale      | Matériel, pilotes, OS, logiciels tiers                         | Administrateur requis |
| **HKU**  | Physique | Globale      | Tous les profils utilisateurs (via SID), écran verrouillage    | Administrateur requis |
| **HKCU** | Virtuel  | Individuelle | Préférences, apparence, mode sombre, applis de l'utilisateur   | Utilisateur standard  |
| **HKCR** | Virtuel  | Mixte        | Associations d'extensions de fichiers (`.txt`), composants COM | Hérité (HKLM/HKCU)    |
| **HKCC** | Virtuel  | Session      | Profil matériel actif au démarrage (affichage, imprimantes)    | Administrateur requis |

| Caractéristique | HKEY_LOCAL_MACHINE (HKLM) | HKEY_CURRENT_USER (HKCU) | HKEY_USERS (HKU) | HKEY_CLASSES_ROOT (HKCR) | HKEY_CURRENT_CONFIG (HKCC) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Type** | Physique | Virtuel (Lien vers HKU) | Physique | Virtuel (Fusion HKLM/HKCU) | Virtuel (Lien vers HKLM) |
| **Portée** | Global (tout l'ordinateur) | Individuel (session ouverte) | Global (tous les profils) | Mixte (système et utilisateur) | Session matérielle en cours |
| **Contenu** | Matériel, pilotes, OS et logiciels tiers | Préférences, apparence, mode sombre | Tous les SID utilisateurs et `.DEFAULT` | Associations d'extensions (`.txt`) et composants COM | Profil matériel actif au démarrage |
| **Droits requis** | Administrateur | Utilisateur standard | Administrateur | Hérité (selon HKLM ou HKCU) | Administrateur |
| **Emplacement** | `C:\Windows\System32\config\` | `C:\Users\[Utilisateur]\NTUSER.DAT` | `C:\Users\` (Profils) & `\System32\config` | Construit dynamiquement en RAM | Construit dynamiquement en RAM |

### Types de données du Registre Windows
Les données du Registre Windows sont stockées dans des formats spécifiques appelés **types de données**. 

1. **REG_SZ (String Value)**
	* **Type :** Chaîne de caractères.
	* **Format :** Texte brut Unicode terminé par un caractère nul.
	* **Utilisation :** Noms, chemins de fichiers, variables d'environnement simples, états textuels (ex: `"C:\Program Files\App\app.exe"` ou `"True"`).

2. **REG_DWORD (Double Word)**
	* **Type :** Entier 32 bits.
	* **Format :** Nombre entier non signé (de `0` à `4 294 967 295`), affiché en hexadécimal ou en décimal.
	* **Utilisation :** Paramètres booléens (0 pour désactivé, 1 pour activé), codes d'erreur, ports réseau, compteurs système.

3. **REG_QWORD (Quad Word)**
	* **Type :** Entier 64 bits.
	* **Format :** Nombre entier non signé de grande taille (de `0` à `18 446 744 073 709 551 615`), affiché en hexadécimal ou en décimal.
	* **Utilisation :** Valeurs numériques volumineuses, grands identifiants de taille/espace disque, compteurs de haute précision, pointeurs mémoire sur architectures 64 bits.

| Caractéristique       | REG_SZ                                 | REG_DWORD                                     | REG_QWORD                                             |
| :-------------------- | :------------------------------------- | :-------------------------------------------- | :---------------------------------------------------- |
| **Nature**            | Texte (Chaîne de caractères)           | Nombre entier (32 bits)                       | Nombre entier (64 bits)                               |
| **Taille en mémoire** | Variable (selon la longueur du texte)  | Fixe : 4 octets                               | Fixe : 8 octets                                       |
| **Plage de valeurs**  | N/A (Texte Unicode)                    | 0 à 4 294 967 295                             | 0 à 18 446 744 073 709 551 615                        |
| **Cas d'usage type**  | Chemins d'accès, commutateurs textuels | Commutateurs binaires (0/1), petits compteurs | Tailles de fichiers géants, compteurs haute précision |

---
### Autres types de données du Registre Windows

4. REG_BINARY
	* **Type :** Données binaires brutes.
	* **Format :** Suite d'octets représentée en hexadécimal.
	* **Utilisation :** Informations de configuration du matériel, hachages de sécurité, signatures numériques ou structures de données complexes internes à l'OS.

4. REG_MULTI_SZ
	* **Type :** Liste de chaînes de caractères.
	* **Format :** Plusieurs chaînes de texte brut regroupées, séparées et terminées par des caractères nuls.
	* **Utilisation :** Listes de pilotes à charger, listes de serveurs DNS, sélections multiples de valeurs réseau ou listes d'applications exclues.

5. REG_EXPAND_SZ
	* **Type :** Chaîne de caractères extensible.
	* **Format :** Texte brut contenant des variables d'environnement système.
	* **Utilisation :** Chemins d'accès dynamiques résolus en direct par l'OS (ex : `%SystemRoot%\System32` ou `%USERPROFILE%\AppData`).

| Caractéristique | REG_SZ        | REG_DWORD          | REG_QWORD        | REG_BINARY      | REG_MULTI_SZ       | REG_EXPAND_SZ     |
| :-------------- | :------------ | :----------------- | :--------------- | :-------------- | :----------------- | :---------------- |
| **Nature**      | Texte brut    | Entier 32 bits     | Entier 64 bits   | Binaire brut    | Liste de textes    | Texte + Variables |
| **Taille**      | Variable      | 4 octets           | 8 octets         | Variable        | Variable           | Variable          |
| **Cas d'usage** | Chemins fixes | États (0/1), ports | Grands compteurs | Config matériel | Listes de serveurs | `%SystemRoot%`    |

---
## Bonnes pratiques de sauvegarde du Registre Windows

1. **Créer un point de restauration système (Le plus sûr)**
	* **Méthode :** Utiliser `sysdm.cpl` ou la commande PowerShell `Checkpoint-Computer`.
	* **Avantage :** Sauvegarde le Registre ET l'état du système. Permet de réparer Windows même si l'OS ne démarre plus (via les options de récupération).

2. **Exportation ciblée (Le plus rapide)**
	* **Règle d'or :** Ne jamais exporter l'intégralité du Registre en fichier `.reg` (trop lourd et génère des erreurs au réimport).
	* **Action :** Exporter uniquement la branche ou la sous-clé spécifique que vous vous apprêtez à modifier.
	* **Procédure :** Clic droit sur la clé dans `regedit` -> **Exporter**.

3. **Automatisation des sauvegardes (Prévention)**
	* **RegBack :** Windows 10/11 ne sauvegarde plus automatiquement le Registre dans le dossier `System32\config\RegBack` par défaut.
	* **Réactivation :** Créer une clé `REG_DWORD` nommée `EnablePeriodicBackup` avec la valeur `1` dans :
	  `HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Configuration Manager\`.

4. **Sauvegarde physique à froid (Scénario catastrophe)**
	* **Principe :** Copier les fichiers sources directement depuis un environnement externe (WinPE, clé USB Live USB, ou autre OS en dual-boot).
	* **Source :** Copier le dossier `C:\Windows\System32\config\` (fichiers SYSTEM, SOFTWARE, SAM) et le fichier `NTUSER.DAT` de l'utilisateur.
	* **Condition :** Le système Windows ciblé doit être totalement arrêté pour libérer les verrous sur les fichiers.

| Méthode de sauvegarde      | Méthode                                                                | Portée / Efficacité                                 |
| :------------------------- | :--------------------------------------------------------------------- | :-------------------------------------------------- |
| **Point de Restauration**  | Outil `rstrui.exe` (accessible même sans session OS via WinRE).        | Intégrale et hautement sécurisée.                   |
| **Fichier `.reg` (Ciblé)** | Double-clic sur le fichier ou commande `reg import fichier.reg`.       | Uniquement la clé modifiée.                         |
| **Dossier `RegBack`**      | Remplacement des fichiers corrompus via l'invite de commandes WinRE.   | Intégrale, remet le registre à une date antérieure. |
| **Copie physique à froid** | Écrasement des fichiers corrompus depuis un environnement WinPE/Linux. | Intégrale, ultime recours si l'OS est corrompu.     |

---
## Commandes et Structure des Fichiers .reg

### Commandes de Sauvegarde et Restauration (PowerShell / CMD)

1. **PowerShell**
	* **Créer un point de restauration :**
	  ```powershell
	  Checkpoint-Computer -Description "AvantModifRegistre" -RestorePointType MODIFY_SETTINGS
	  ```
	  
	* **Exporter une clé spécifique :**
	  ```powershell
	  Export-RegistryValue -Path "HKLM:\SOFTWARE\MyApp" -Destination "C:\Backup\MyApp.reg"
	  ```
	  
	* **Importer un fichier `.reg` :**
	  ```powershell
	  reg import "C:\Backup\MyApp.reg"
	  ```

2. **CMD**
	* **Exporter une clé spécifique :**
	  ```cmd
	  reg export "HKLM\SOFTWARE\MyApp" "C:\Backup\MyApp.reg" /y
	  ```
	  
	* **Importer un fichier `.reg` :**
	  ```cmd
	  reg import "C:\Backup\MyApp.reg"
	  ```
	  

### Structure et Syntaxe fichier `.reg`
Un fichier `.reg` est un fichier texte brut encodé en UTF-16 LE (généralement) qui automatise les modifications du Registre.

1. **En-tête obligatoire**
	Le fichier doit obligatoirement commencer par cette ligne exacte, suivie d'une ligne vide :
	```text
	Windows Registry Editor Version 5.00
	```

2. **Ajouter ou Modifier une clé / valeur**
	* **Syntaxe :** Le chemin de la clé est entre crochets `[...]`. Les valeurs sont sous la forme `"Nom"=[Type]:[Donnée]`.
	* **Exemple :**
	  ```text
	  [HKEY_CURRENT_USER\Software\MonApplication]
	  "Version"="1.0"
	  "Actif"=dword:00000001
	  ```

3. **Supprimer une valeur spécifique**
	* **Syntaxe :** Placer un signe moins `-` juste après le signe `=` de la valeur.
	* **Exemple :**
	  ```text
	  [HKEY_CURRENT_USER\Software\MonApplication]
	  "Version"=-
	  ```

4. **Supprimer une clé complète (et toutes ses sous-clés)**
	* **Syntaxe :** Placer un signe moins `-` juste après le crochet ouvrant `[-...]`.
	* **Exemple :**
	  ```text
	  [-HKEY_CURRENT_USER\Software\MonApplication]
	  ```

| Action souhaitée                        | Syntaxe dans le fichier `.reg`                       |
| :-------------------------------------- | :--------------------------------------------------- |
| **Créer / Modifier clé**                | `[HKEY_CURRENT_USER\Chemin\De\La\Cle]`               |
| **Créer / Modifier chaîne (REG_SZ)**    | `"NomValeur"="Texte"`                                |
| **Créer / Modifier entier (REG_DWORD)** | `"NomValeur"=dword:00000001` (valeur en hexadécimal) |
| **Supprimer une valeur**                | `"NomValeur"=-`                                      |
| **Supprimer une clé**                   | `[-HKEY_CURRENT_USER\Chemin\De\La\Cle]`              |

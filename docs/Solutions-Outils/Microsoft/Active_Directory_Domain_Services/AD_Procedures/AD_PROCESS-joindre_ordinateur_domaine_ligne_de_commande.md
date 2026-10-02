---
tags:
  - Procédure
  - ADDS
  - Admin
  - IT
  - Système
  - Microsoft
---
1. **Introduction et prérequis**
	- Un ordinateur peut être joint à un domaine par interface graphique, ligne de commande ou PowerShell.
	- Prérequis : configuration IP correcte (DNS pointant sur le contrôleur), être administrateur local, et avoir un compte de domaine autorisé.

2. **Jonction avec NETDOM**
	- Disponible nativement sur Windows Server, la commande s'exécute sur le poste à joindre.
	- Syntaxe :
	```cmd
	netdom join <ordinateur> /domain:<domaine> /ud:<utilisateur> /pd:<mot_de_passe>
	```
	
	- Exemple :
	```cmd
	netdom join SRVCORE2 /domain:lab.intra /ud:LAB\administrateur /pd:*
	```
	
	- Options possibles : /OU pour spécifier l'unité d'organisation, /reboot pour redémarrer.

3. **Jonction hors-ligne avec DJOIN**
	Permet de joindre un domaine sans connexion directe en deux étapes.
	- **Étape 1 - sur le contrôleur** : provisionner le poste et créer un fichier de métadonnées.
	- Commande :
	```cmd
	djoin /provision /domain <domaine> /machine <ordinateur> /savefile <chemin_fichier>
	```
	
	- Exemple :
	```cmd
	djoin /provision /domain LAB /machine NanoSrv /savefile C:\SrvNanoJoin
	```
	
	- **Étape 2 - sur le poste** : importer le fichier pour finaliser la jonction au prochain redémarrage.
	- Commande :
	```cmd
	djoin /requestodj /loadfile <chemin_fichier> /windowspath %systemroot% /localos
	```
	
	- Exemple :
	```cmd
	djoin /requestodj /loadfile C:\SrvNanoJoin /windowspath C:\Windows /localos
	```

4. **Jonction avec PowerShell**
	- S'exécute dans une invite PowerShell sur l'ordinateur à joindre.
	- Commande :
	```powershell
	Add-Computer -DomainName <domaine> -Credential <utilisateur>
	```
	
	- Exemple :
	```powershell
	Add-Computer -DomainName lab.intra -Credential administrateur@lab.intra
	```
	- Action requise : saisir le mot de passe puis redémarrer l'ordinateur.

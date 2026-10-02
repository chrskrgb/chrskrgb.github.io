---
tags:
  - Procédure
  - ADDS
  - Admin
  - IT
  - Système
  - Microsoft
---
## Mono DC

1. **Diagnostics & Prérequis**
	- Constat : Incohérence entre le suffixe DNS du serveur et le nom réel du domaine Active Directory.
	- Vérification AD : La commande `repadmin /replsummary` renvoie un résultat vide (confirmation du DC unique).
	- Sauvegarde : Exécuter un backup System State récent de l'état du système.
	- Rôles FSMO : Confirmer la disponibilité et la santé des rôles FSMO.

2. **Alignement du Suffixe DNS**
	- Ouvrir les propriétés système via `Win + R` -> `sysdm.cpl`.
	- Cliquer sur Modifier -> Plus.
	- Remplacer le suffixe DNS principal erroné par le nom exact du domaine AD.
	- Valider les modifications et redémarrer immédiatement le serveur.

3. **Processus de renommage** (CMD Admin)
	- Ajouter le nouveau FQDN cible comme nom alternatif :
	```cmd
	netdom computername <Ancien-FQDN> /add:<Nouveau-FQDN>
	```
	
	- Permuter le nom alternatif pour le définir en tant que nom principal :
	```cmd
	netdom computername <Ancien-FQDN> /makeprimary:<Nouveau-FQDN>
	```
	
	==Redémarrer obligatoirement le serveur pour appliquer le changement de nom==.
	
	- **Supprimer définitivement l'ancien nom obsolète** :
	```cmd
	netdom computername <Nouveau-FQDN> /remove:<Ancien-FQDN>
	```

4. **Diagnostics & Validations de santé** (CMD Admin)
	- Analyser la santé globale du DC : 
	```cmd
	dcdiag /v
	```
	
	- Tester la connectivité générale : 
	```cmd
	dcdiag /test:connectivity
	```
	
	- Valider la publication des enregistrements SRV : 
	```cmd
	dcdiag /test:registerindns /dnsdomain:<Nom-Domaine>
	```
	
	- Forcer la mise à jour des enregistrements réseau : 
	```cmd
	ipconfig /registerdns
	```

5. **Script de vérification DNS** (PowerShell Admin)
	- Valider la résolution locale :
	```powershell
	Resolve-DnsName -Name "<Nouveau-FQDN>" -Type A
	Resolve-DnsName -Name "<Nom-Domaine>" -Type A
	Resolve-DnsName -Name "_ldap._tcp.dc._msdcs.<Nom-Domaine>" -Type SRV
	Resolve-DnsName -Name "_kerberos._tcp.dc._msdcs.<Nom-Domaine>" -Type SRV
	```
	
	- Les deux premières doivent renvoyer l'IP du serveur, les deux suivantes doivent pointer vers le nouveau FQDN.

6. **Nettoyage**
	- `dnsmgmt.msc` : Supprimer les anciens enregistrements A et SRV qui pointent encore vers l'ancien nom.
	- `dsa.msc` : Vérifier dans l'OU "Domain Controllers" que l'attribut `dnsHostName` affiche le nouveau FQDN.
	- `eventvwr.msc` : Inspecter les journaux Directory Service et DNS Server (absence d'erreur ID 4013).
	- Modifier le ciblage des services tiers utilisant des liaisons LDAP, NPS ou Radius vers le nouveau FQDN.


## Multi-DC

1. **Diagnostics & Prérequis stricts**
	- Vérification AD : La commande `repadmin /replsummary` affiche tous les DC. La réplication doit afficher 0 échec.
	- Sauvegarde : Réaliser un backup System State récent de TOUS les contrôleurs de domaine.
	- Rôles FSMO : Si le DC à renommer possède des rôles FSMO, les transférer temporairement vers un autre DC stable.

2. **Alignement du Suffixe DNS**
	- Ouvrir les propriétés système via `Win + R` -> `sysdm.cpl`.
	- Cliquer sur Modifier -> Plus.
	- S'assurer que le suffixe DNS principal correspond exactement au nom du domaine AD.
	- Valider et redémarrer le serveur.

3. **Processus de renommage** (CMD Admin)
	- Ajouter le nouveau FQDN alternatif :
	```cmd
	netdom computername <Ancien-FQDN> /add:<Nouveau-FQDN>
	```
	
	- Forcer la réplication pour propager le nom alternatif aux autres DC :
	```cmd
	repadmin /syncall /AdP
	```
	
	- Permuter pour définir le nouveau nom en principal :
	```cmd
	netdom computername <Ancien-FQDN> /makeprimary:<Nouveau-FQDN>
	```
	
	==Redémarrer obligatoirement le serveur==.
	==Attendre la réplication complète du reboot sur les autres DC avant de continuer==.
	
	- Supprimer définitivement l'ancien nom obsolète :
	```cmd
	netdom computername <Nouveau-FQDN> /remove:<Ancien-FQDN>
	```
	
	- Forcer à nouveau la réplication pour finaliser la suppression partout :
	```cmd
	repadmin /syncall /AdP
	```

4. **Diagnostics & Validations de groupe** (CMD & PowerShell Admin)
	- Analyser la santé du DC renommé : 
	```cmd
	dcdiag /v
	```
	
	- Valider la réplication globale : 
	```cmd
	repadmin /replsummary
	```
	
	- Vérifier la convergence DNS globale :
	```powershell
	Resolve-DnsName -Name "<Nouveau-FQDN>" -Type A
	Resolve-DnsName -Name "_ldap._tcp.dc._msdcs.<Nom-Domaine>" -Type SRV
	```

5. **Nettoyage**
	- `dnsmgmt.msc` : Se connecter sur chaque DC et forcer le nettoyage des enregistrements A et SRV obsolètes.
	- `dsa.msc` : Vérifier la mise à jour de l'attribut `dnsHostName` sur le DC modifié depuis la console d'un autre DC.
	- `eventvwr.msc` : Surveiller l'apparition d'erreurs de réplication (KCC / NTDS Replication) sur l'ensemble des contrôleurs de domaine.

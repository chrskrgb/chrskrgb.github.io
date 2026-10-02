---
tags:
  - Note
  - ADDS
  - Admin
  - IT
  - Système
  - Microsoft
---
## Tiering Model : les fondamentaux 

1. **Présentation et contexte**
   - **Définition :** segmentation stricte des comptes à privilèges en couches indépendantes.
   - Recommandé par l'**`ANSSI`** et **Microsoft**.
   - S'inscrit dans la stratégie globale de gestion des accès à privilèges (**`PAM`**).
   - Réduit la surface d'attaque en limitant l'impact d'une compromission de compte.

2. **Limites de l'administration traditionnelle**
   - Utilisation d'un compte unique membre d'**`Admins du domaine`** pour toutes les tâches.
   - Risque majeur : compromission totale du système d'information (**`SI`**).
   - Mécanisme d'attaque :
     - Infection initiale d'un poste de travail par un logiciel malveillant.
     - Récupération des identifiants d'administration en mémoire ou dans le **Registre**.
     - Déplacement latéral sans restriction et élévation de privilèges.

3. **Rôle du Privileged Access Management (`PAM`)**
   - Application rigoureuse du **moindre privilège**.
   - Restriction des accès aux seules actions nécessaires.
   - Gestion des accès temporaires et audit continu des activités administratives.

4. **Principe du modèle en couches**
   - Cloisonnement étanche des identités par périmètre fonctionnel.
   - Interdiction formelle d'authentification d'un compte de niveau inférieur sur un actif de niveau supérieur.
   - Endiguement des attaques pour éviter la propagation à l'ensemble du domaine.

5. **Architecture des trois niveaux (`Tiers`)**
   - **Tier 0** (actifs critiques et gestion des identités) :
     - Contrôleurs de domaine (`DC`).
     - Autorités de certification **`AD CS`** (`PKI`).
     - Serveurs d'authentification **`RADIUS`** et **`NPS`**.
     - Outils de synchronisation d'identité (**`Microsoft Entra Connect`**, **`Google Cloud Directory Sync`**).
     - Services de fédération **`ADFS`**.
     - Hyperviseurs intégrés au domaine et sauvegardes transverses.
     - Équipements réseau (switches) isolés via **`VLAN`** dédié ou rattachés par défaut au **Tier 0**.
    
   - **Tier 1** (serveurs d'infrastructure et métiers) :
     - Serveurs applicatifs.
     - Serveurs de bases de données.
     - Serveurs de fichiers.
    
   - **Tier 2** (environnement utilisateur) :
     - Postes de travail fixes et portables.
     - Imprimantes et périphériques.

6. **Interactions et règles de gestion des comptes**
   - **Restriction majeure :** étanchéité absolue entre chaque silo d'identité.
   - Répartition obligatoire d'au moins quatre comptes par administrateur :
     - Compte utilisateur standard pour la bureautique courante.
     - Compte d'administration dédié pour le **Tier 0**.
     - Compte d'administration dédié pour le **Tier 1**.
     - Compte d'administration dédié pour le **Tier 2**.
    
   - Délégation granulaire selon les rôles : **Opérateur**, **Administrateur** et **Manager**.
   - En cas d'attaque sur le **Tier 2**, les mouvements latéraux restent confinés aux postes clients.

7. **Mesures complémentaires et outillage**
   - Le modèle en couches intervient en fin de durcissement et ne corrige pas les failles sous-jacentes.
   - Vecteurs résiduels : protocoles obsolètes, vulnérabilités applicatives et défauts de configuration.
   - Outils d'audit recommandés :
     - **`Ping Castle`**, **`Purple Knight`** et **`GPOZaurr`** pour l'analyse globale de l'annuaire.
     - **`HardenSysvol`** et **`Snaffler`** pour la détection de secrets dans les partages **`SYSVOL`**.
     - **`BloodHound`** pour cartographier les chemins d'attaque.
    
   - Solutions de déploiement et d'automatisation :
     - **`HardenAD`** pour préparer l'architecture, intégrer un **Tier Legacy** et désactiver les protocoles obsolètes (**`SMBv1`**, **`LLMNR`**, **`NTLMv1`**).
     - **`Hello My DIR!`** pour générer des architectures sécurisées dès la création du domaine.

8. **Bonnes pratiques d'exploitation**
   - Application technique des restrictions par stratégies de groupe directes sans reposer sur la seule confiance humaine.
   - Déploiement de machines de rebond durcies dédiées par niveau d'administration.
   - Utilisation de bastions d'administration pour la traçabilité des flux (**`Apache Guacamole`**, **`Teleport`**).

---
## Microsoft AD Tier Model vs HardenAD

1. **Présentation et contexte de publication**
   - Publication en juillet 2026 par Joel Platek d'un module `PowerShell` nommé **`Active Directory Tier Model`**.
   - Réactivation d'une approche on-premises inspirée du modèle **`ESAE`** simplifié.
   - Segmentation stricte en trois niveaux :
     - **Tier 0** (identités et composants critiques).
     - **Tier 1** (serveurs d'infrastructure et métiers).
     - **Tier 2** (postes clients, utilisateurs et périphériques).
    
   - Exécution sous **`PowerShell 7+`** et dépendance à **`Pester 5.7.1`** depuis une station d'administration.
   - Intégration conçue pour les pipelines CI/CD (**`Azure`** et **`GitHub Actions`**).

2. **Points forts et volume des déploiements Microsoft**
   - Réalisation de 664 actions automatisées au déploiement :
     - Création de 31 Unités d'Organisation (**`OU`**).
     - Création de 26 groupes de sécurité.
     - Déploiement de 101 listes de contrôle d'accès (**`ACL`**).
     - Importation et liaison de 146 stratégies de groupe (**`GPO`**).
     - Importation de 30 modèles d'administration **`ADMX`** et **`ADML`**.
    
   - Purge explicite de 22 privilèges sensibles du système (ex. : `SeTcbPrivilege`, `SeCreateToken`, `SeLockMemory`).
   - Baselines de sécurité exhaustives pour les environnements 100 % Microsoft :
     - **`Windows 11`** et **`Windows Server 2025`**.
     - **`Credential Guard`**, **`VBS`**, durcissement **`Kerberos`** et **`SMB 3.0+`**.
     - Tagging natif pour **`Microsoft Defender for Endpoint`** (`MDE`).
     - Configuration stricte des applications **`Microsoft 365`** (blocage des macros et `DDE`).

3. **Anomalies de déploiement et limites linguistiques**
   - Échec à l'installation lié à la vérification des prérequis :
     - Comparaison sous forme de chaîne de caractères (`string`) au lieu d'une comparaison numérique.
     - Contournement : forcer l'installation de la version `5.7.1` de **`Pester`** ou modifier la cible en `6.0.1`.
   - Absence de support multilingue :
     - Modèles **`ADML`** fournis exclusivement en anglais (`en-US`).
     - Risques d'incompatibilité avec les environnements localisés exploitant des comptes natifs traduits.

4. **Gestion de l'attribut UserPrincipalName (`UPN`)**
   - Comptes de service créés sans valeur déclarative pour l'attribut `userPrincipalName`.
   - Application d'un **`UPN`** implicite au format `sAMAccountName@DnsDomainName`.
   - Avantages sécuritaires :
     - Protection contre les attaques d'usurpation via **`AD CS`** reposant sur les droits `GenericWrite`.
     - Élimination des collisions inter-domaines et des conflits avec `sAMAccountName`.
     - Réduction de la surface d'exposition et d'énumération sur le **Tier 0**.
   - Impacts opérationnels négatifs :
     - Blocage de la synchronisation avec **`Entra ID`** et des services **`M365`**.
     - Problèmes d'authentification pour les services exploitant le champ `UPN in SAN` (**`802.1X`**, **`VPN`**, cartes à puce).
     - Rupture de liaison avec les applications tierces dépendantes de l'attribut en **`LDAP`**, **`SAML`** ou **`SCIM`**.

5. **Utilisation des groupes universels et silos d'authentification**
   - Déclaration de quatre groupes universels dédiés aux serveurs membres et stations **`PAW`** des **Tier 0** et **Tier 1**.
   - Fonction de jointure inter-domaines pour le ciblage des silos et des flux chiffrés **`IPsec`**.
   - Risques techniques associés :
     - Augmentation de la taille du jeton Kerberos (`Token Bloat` via surcharge du champ `PAC`).
     - Dépendance stricte au Catalogue Global (**`GC`**) lors de l'évaluation des règles d'accès.
     - Risque de verrouillage d'authentification d'urgence si le **`GC`** est inaccessible.
    
   - Prérequis indispensables :
     - Niveau fonctionnel supérieur ou égal à **`Windows Server 2012 R2`**.
     - Activation par **`GPO`** du support KDC pour les revendications (`Claims`), l'authentification composée et l'armement Kerberos.

6. **Politique d'application des `GPO` et risques d'héritage**
   - Recours massif et systématique au blocage de l'héritage (`Block Inheritance`) :
     - Pratique formellement déconseillée par les recommandations d'administration **Microsoft**.
     - Complexification sévère du dépannage des stratégies.
     - Absence du paramètre forcé (`Enforced`) sur les stratégies filles, autorisant l'écrasement des paramètres de sécurité.
    
   - Absence de conteneur d'intégration sécurisé :
     - Assignation des nouvelles machines au conteneur par défaut non couvert par le modèle.
     - Application automatique d'une **`GPO`** de racine restrictive risquant de bloquer l'administration locale.

7. **Lacunes de conception du modèle Microsoft**
   - Absence totale de prise en charge de la dette technique :
     - Modèle réservé aux annuaires récents sans gestion des systèmes obsolètes.
    
   - Omission des stratégies de mot de passe affinées (**`PSO`**).
   - Incohérence du groupe **`Protected Users`** laissé totalement vide par défaut.
   - Granularité insuffisante des délégations administratives :
     - Perméabilité entre l'administration des objets d'annuaire et la gestion opérationnelle des serveurs en **Tier 1**.
     - Absence de directives de restriction d'ouverture de session (`Deny Logon`) pour les administrateurs d'objets sur les serveurs.
     - Persistance des identifiants via le maintien actif du cache des sessions.

8. **Résultats d'audit et comparaison avec HardenAD**
   - Évaluation comparative sur banc d'essai **`Ping Castle`** :
     - Annuaire d'origine : score de 40/100 (maturité 2/5).
     - Après déploiement Microsoft : maintien du score à 40/100.
     - Après déploiement **`HardenAD`** : réduction du score de risque à 30/100 (et jusqu'à 15/100 après durcissement de la corbeille **`AD`** et des comptes sensibles).
    
   - Différenciateurs majeurs d'**`HardenAD`** :
     - Intégration native de couches dédiées aux parcs hétérogènes (**Tier 1 Legacy** et **Tier 2 Legacy**).
     - Déploiement progressif, modulaire et hautement personnalisable sans rupture de production.
     - Prise en compte de l'hybridation cloud avec exclusion via unités organisationnelles dédiées (`DoNotSync`).
     - Application de stratégies de groupe strictement forcées (`Enforced`) sans rupture de l'héritage d'annuaire.

---
## Sécurisation AD et analyse du Tiering Model

1. **Principes et architecture du Tiering Model**
- **Définition :** segmentation stricte des identités et des actifs en couches étanches pour confiner les compromissions.
- S'appuie sur les recommandations de l'**`ANSSI`**, de **Microsoft** et les standards **`PAM`**.
- Structure pyramidale en trois niveaux principaux :
	- **Tier 0** (contrôle des identités et actifs critiques) :
		- Contrôleurs de domaine (`DC`), **`AD CS`** (`PKI`), **`RADIUS`**, **`NPS`**, **`ADFS`**.
		- Outils de synchronisation d'identité (**`Entra Connect`**, **`GCDS`**), hyperviseurs et sauvegardes transverses.
		- Équipements réseau rattachés au **Tier 0** à défaut d'isolation stricte sur un **`VLAN`** dédié.
		
	- **Tier 1** (serveurs d'infrastructure et serveurs métiers) :
		- Serveurs applicatifs, bases de données et serveurs de fichiers.
		
	- **Tier 2** (environnement utilisateur final) :
		- Postes de travail fixes ou nomades et périphériques d'impression.

2. **Règles d'étanchéité et gestion des comptes**
	- **Restriction majeure :** interdiction absolue d'authentification descendante ou montante entre les niveaux.
	- Exigence minimale de quatre comptes distincts par administrateur :
		- Un compte standard pour la bureautique quotidienne et les flux Internet.
		- Un compte dédié et restreint pour chaque niveau d'intervention (**Tier 0**, **Tier 1**, **Tier 2**).
		
	- Confinement des mouvements latéraux au seul niveau compromis en cas d'attaque initiale sur un poste client.

3. **Mesures d'hygiène et outillage d'audit préalable**
	- Le modèle en couches ne dispense pas de la correction des failles intrinsèques du système d'information (**`SI`**).
	- Outils d'évaluation et de détection recommandés :
		- **`Ping Castle`**, **`Purple Knight`** et **`GPOZaurr`** pour l'audit d'architecture et de configuration.
		- **`HardenSysvol`** et **`Snaffler`** pour la détection d'identifiants exposés dans le partage **`SYSVOL`**.
		- **`BloodHound`** pour la mise en évidence des chemins d'attaque.
		
	- Durcissement périphérique : désactivation des protocoles obsolètes (**`SMBv1`**, **`LLMNR`**, **`NTLMv1`**) et usage de bastions (**`Teleport`**, **`Apache Guacamole`**).z

4. **Caractéristiques du module Microsoft Active Directory Tier Model**
	- Publication en juillet 2026 par Joel Platek d'un module d'automatisation sous **`PowerShell 7+`** et **`Pester 5.7.1`**.
	- Déploiement massif de 664 actions standardisées :
		- Création de 31 Unités d'Organisation (**`OU`**), 26 groupes et 101 listes de contrôle d'accès (**`ACL`**).
		- Application de 146 stratégies de groupe (**`GPO`**) et importation de 30 modèles **`ADMX`** / **`ADML`**.
		
	- Apports de sécurité majeurs :
		- Purge stricte de 22 privilèges critiques (ex. : `SeTcbPrivilege`, `SeCreateToken`, `SeLockMemory`).
		- Baselines complètes pour **`Windows 11`**, **`Windows Server 2025`**, **`Credential Guard`**, **`VBS`** et **`M365 Apps`**.
		- Balisage natif pour **`Microsoft Defender for Endpoint`** (`MDE`).

5. **Limites techniques et risques du modèle Microsoft**
	- Bugs et contraintes d'installation :
		- Erreur bloquante sur la validation de version de **`Pester`** due à une comparaison de chaînes de caractères.
		- Modèles **`ADML`** fournis exclusivement en anglais (`en-US`), posant des risques sur les systèmes localisés.
		
	- Gestion de l'attribut `userPrincipalName` (**`UPN`**) :
		- Absence d'**`UPN`** explicite sur les comptes de service pour limiter l'exposition **`AD CS`** et éviter les collisions.
		- Effets de bord bloquants : rupture de l'authentification `UPN in SAN` (**`802.1X`**, **`VPN`**) et synchronisation **`Entra ID`** impossible.
		
	- Utilisation des groupes universels et silos d'authentification :
		- Risques d'engorgement du jeton Kerberos (`Token Bloat` via le `PAC`).
		- Dépendance critique à la disponibilité continue du Catalogue Global (**`GC`**).
		
	- Faiblesses architecturales constatées :
		- Usage systématique du blocage d'héritage de **`GPO`** (`Block Inheritance`) sans forçage (`Enforced`), contraire aux bonnes pratiques.
		- Aucune gestion des systèmes obsolètes (absence de composante **Legacy**).
		- Absence de stratégies de mot de passe affinées (**`PSO`**) et groupe **`Protected Users`** laissé vide par défaut.
		- Persistance du cache de session et étanchéité incomplète des privilèges d'administration en **Tier 1**.

6. **Comparatif opérationnel avec la solution HardenAD**
	- Résultats d'évaluation sur banc de test **`Ping Castle`** :
		- Score initial : 40/100 (maturité 2/5).
		- Après déploiement Microsoft : maintien du score à 40/100 (outillage orienté reconstruction rapide).
		- Après déploiement **`HardenAD`** : amélioration à 30/100 (et jusqu'à 15/100 après durcissement de la corbeille et des comptes).
		
	- Avantages différentiateurs d'**`HardenAD`** :
		- Intégration structurelle de la dette technique via des silos dédiés (**Tier 1 Legacy** et **Tier 2 Legacy**).
		- Déploiement modulaire et progressif adapté aux annuaires en production complexes.
		- Compatibilité native avec le cloud via des unités organisationnelles d'exclusion (`DoNotSync`).
		- Préservation de l'héritage d'annuaire combiné à des stratégies strictement forcées (`Enforced`).
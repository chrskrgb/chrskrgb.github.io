---
tags:
  - Note
  - ADDS
  - Admin
  - IT
  - Système
  - Microsoft
---
L'extension **.msc** est un fichiers de configuration utilisés par la **Microsoft Management Console (MMC)**.

### Comment les utiliser ?
Via la boîte de dialogue **Exécuter** :
- **Windows + R**.
- Tapez le nom du fichier (ex: `services.msc`).
- **Entrée**.

### Pourquoi utiliser les .msc plutôt que les "Paramètres" ?
Depuis Windows 10 et 11, Microsoft tente de simplifier l'interface avec l'application "Paramètres". Cependant, les fichiers **.msc** restent privilégiés par les techniciens et administrateurs car :
- **Précision** : Ils offrent des options beaucoup plus granulaires.
- **Rapidité** : Une commande de 10 lettres est souvent plus rapide que 5 clics dans des menus tactiles.
- **Légèreté** : Ces consoles consomment très peu de ressources système.

> **Astuce** : créer sa propre console personnalisée en tapant simplement `mmc` dans la barre de recherche, puis en allant dans _Fichier > Ajouter/Supprimer un composant logiciel enfichable_. Cela vous permet de regrouper tous vos outils favoris dans un seul fichier `.msc`.

#### 🛠️ Administration Générale et Système
- **compmgmt.msc** : Gestion de l'ordinateur (la console "tout-en-un").
- **devmgmt.msc** : Gestionnaire de périphériques (pilotes et matériel).
- **eventvwr.msc** : Observateur d'événements (journaux d'erreurs et logs).
- **services.msc** : Gestion des services Windows (programmes d'arrière-plan).
- **taskschd.msc** : Planificateur de tâches (automatisation).
- **perfmon.msc** : Analyseur de performances.
- **resmon.msc** : Moniteur de ressources (CPU, RAM, Disque, Réseau en temps réel).

#### 💾 Stockage et Fichiers
- **diskmgmt.msc** : Gestion des disques (partitions, formatage).
- **fsmgmt.msc** : Dossiers partagés (gestion des partages réseau actifs).

#### 👤 Utilisateurs et Sécurité
- **lusrmgr.msc** : Utilisateurs et groupes locaux.
- **gpedit.msc** : Éditeur de stratégie de groupe locale (versions Pro/Entreprise).
- **secpol.msc** : Stratégie de sécurité locale.
- **certlm.msc** : Certificats (Ordinateur local).
- **certmgr.msc** : Certificats (Utilisateur actuel).
- **wf.msc** : Pare-feu Windows avec fonctions avancées de sécurité.

#### 🌐 Réseau et Serveurs
- **azman.msc** : Gestionnaire d'autorisations.
- **printmanagement.msc** : Gestion de l'impression (si installé).

#### 🔑 Active Directory (outils spécifiques)
Si votre machine est un contrôleur de domaine ou possède les outils RSAT :
- **dsa.msc** : Utilisateurs et ordinateurs Active Directory.
- **dnsmgmt.msc** : Gestionnaire DNS.
- **adsiedit.msc** : Éditeur ADSI (modification brute de l'annuaire).
- **gpmc.msc** : Console de gestion des stratégies de groupe (GPO).
- **domain.msc** : Domaines et approbations Active Directory.
---
title: Pare-feu & Durcissement Réseau
description: Bonnes pratiques de règles de filtrage pare-feu, segmentation et audit de sécurité
---

# Pare-feu & Durcissement Réseau

Une protection réseau efficace repose sur la granularité des flux et l'absence d'interconnexions directes entre les zones non sécurisées et le cœur du système d'information.

---

## 1. Matrice de Flux et Modèle de Filtrage

Pour chaque nouveau service, une matrice des flux réseau doit être validée avant ouverture :

| Zone Source | Zone Destination | Port / Protocole | Justification Métier | Statut |
| :--- | :--- | :--- | :--- | :--- |
| **VLAN-DMZ** | **VLAN-PROD-APP** | `TCP / 443` | Reverse Proxy vers App Server | Autorisé |
| **VLAN-PROD-APP** | **VLAN-PROD-DB** | `TCP / 5432` | Accès PostgreSQL applicatif | Autorisé |
| **VLAN-USERS** | **VLAN-PROD-DB** | Tous | Pas d'accès direct autorisé | **Bloqué** |
| **Toutes zones** | **VLAN-ADM (T0)** | Tous | Accès administration direct | **Bloqué (rebond requis)** |

---

## 2. Principes de Bastion & Rebond d'Administration

- Les postes d'administration ne doivent pas accéder directement aux machines de production sensibles.
- Passage obligatoire par un **Serveur de Rebond (Bastion SSH / Guacamole)** avec journalisation des sessions et authentification multi-facteurs (MFA).

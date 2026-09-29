---
title: Scripts & Outillage - Vue d'ensemble
description: Boîte à outils et bibliothèques de scripts pour l'automatisation système et réseau
---

# Scripts & Outillage d'Automatisation

Cette section rassemble des scripts opérationnels réutilisables, conçus pour automatiser des tâches récurrentes de maintenance, de diagnostic et de supervision.

## :material-folder-multiple: Sommaire de la section

<div class="grid cards" markdown>

-   :material-powershell:{ .lg .middle } __[PowerShell](powershell.md)__

    ---

    Scripts d'administration Microsoft 365, requêtes Microsoft Graph et gestion Active Directory.

-   :material-bash:{ .lg .middle } __[Bash & Shell](bash.md)__

    ---

    Automatisation Linux, sauvegardes automatisées, rotation des logs et vérification d'intégrité.

</div>

---

!!! tip "Standardisation & Qualité du code"
    Tous les scripts documentés respectent des règles strictes :
    - Gestion systématique des erreurs (`try/catch` ou `set -euo pipefail`).
    - Sortie journalisée et horodatée.
    - Idempotence lorsque l'opération le permet.

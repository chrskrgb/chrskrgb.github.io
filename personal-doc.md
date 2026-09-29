## Synthèse du Projet Portfolio DevOps

**1. Concept et Stack Technique**
- Objectif : Déployer un site vitrine professionnel à **0€** via l'approche **Docs-as-Code**.
- Générateur de site : `MkDocs` associé au thème **Material**.
- Hébergement : `GitHub Pages` (domaine gratuit et certificat `HTTPS`).
- Déploiement continu (`CI/CD`) : `GitHub Actions` pour l'automatisation à chaque `push`.

**2. Architecture et Format**
- **Avantage principal :** Le format **Wiki** (Documentation technique) est recommandé face au **Blog** pour cibler efficacement les recruteurs.
- Configuration : Fichier central `mkdocs.yml`.
- Arborescence découpée par expertises :
    - Cloud, Conteneurs & Automatisation (`Docker`, `Kubernetes`, `Ansible`).
    - Administration Systèmes & Identités (`Entra ID`, `AD`, `Linux`).
    - Sécurité & Réseaux (`Firewalls`, Hardening).
    - Scripts & Outillage (`PowerShell`, `Bash`).

**3. Intégration du Profil**
- Page d'accueil : Fichier `index.md` généré à partir des données du **CV**.
- Positionnement : Transition de l'**Administration Système** vers l'**Ingénierie DevOps**.
- Navigation : Intégration de boutons interactifs via la classe `.md-button`.

**4. Documentation et Procédures Techniques**
- Modèle de rédaction : Utilisation de la syntaxe `Markdown` enrichie (`!!! warning`, `!!! note`).
- Cas pratique documenté : Sécurisation d'accès sous `Entra ID`.
- Étapes de la procédure :
    - Configuration des prérequis et des comptes **Break-Glass**.
    - Interdiction des protocoles d'authentification héritée.
    - Imposition du `MFA` via les stratégies d'**Accès Conditionnel**.
    - Vérification via les journaux de supervision.

**5. Outils et Environnement de Développement (IDE)**
- **Restriction majeure :** `antigravity` et l'adresse `https://antigravity.google/` ne correspondent à aucun **IDE** vérifiable (il s'agit d'un *easter egg* natif à `Python`).
- Recommandation finale : Privilégier **Visual Studio Code** ou **VSCodium** pour éditer le projet, intégrer `Git` et prévisualiser le site localement.
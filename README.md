# Portfolio Technique & Documentation DevOps (Docs-as-Code)

Projet de site vitrine et documentation technique professionnelle déployé à **0€** sur **GitHub Pages** via **MkDocs Material** et **GitHub Actions**.

---

## 🚀 Démarrage Rapide

### 1. Prérequis
- Python 3.10+ installé.
- Git configuré.

### 2. Installation locale

```bash
# Installation des dépendances
pip install -r requirements.txt
```

*(Optionnel mais recommandé) : Dans un environnement virtuel :*
```bash
python3 -m venv .venv
source .venv/bin/activate  # Sur Linux/macOS
# ou .venv\Scripts\activate sous Windows
pip install -r requirements.txt
```

### 3. Lancer la prévisualisation en direct (Live Reload)

```bash
mkdocs serve
```

Ouvrez ensuite votre navigateur sur **`http://127.0.0.1:8000`**. Tout changement dans les fichiers `.md` ou `mkdocs.yml` sera rechargé instantanément.

---

## 📁 Structure du Projet

```text
personal-doc/
├── .github/
│   └── workflows/
│       └── deploy.yml           # Pipeline CI/CD GitHub Pages
├── docs/                        # Contenu du site (Markdown)
│   ├── index.md                 # Page d'accueil du portfolio
│   ├── a-propos.md              # Présentation & CV
│   ├── stylesheets/
│   │   └── extra.css            # Styles CSS personnalisés
│   ├── cloud-devops/            # Catégorie Cloud, Docker, K8s, Ansible
│   │   ├── index.md
│   │   ├── docker-k8s.md
│   │   └── ansible.md
│   ├── systeme-identite/        # Catégorie Entra ID, AD, Linux
│   │   ├── index.md
│   │   ├── entra-id-securisation.md
│   │   ├── active-directory.md
│   │   └── linux.md
│   ├── securite-reseau/         # Catégorie Sécurité, Pare-feu, Hardening
│   │   ├── index.md
│   │   └── firewalls-hardening.md
│   └── scripts-outillage/       # Catégorie Scripts PowerShell & Bash
│       ├── index.md
│       ├── powershell.md
│       └── bash.md
├── mkdocs.yml                   # Configuration centrale et arborescence du site
├── requirements.txt             # Dépendances Python (MkDocs + Material)
└── README.md
```

---

## 🛠️ Modifier l'Arborescence du Site

Pour réorganiser, renommer ou ajouter des rubriques :

1. **Ajouter ou modifier des fichiers Markdown** dans le dossier `docs/`.
2. **Adapter la clé `nav`** dans le fichier [`mkdocs.yml`](mkdocs.yml) :

```yaml
nav:
  - Accueil: index.md
  - Votre Nouvelle Section:
      - "Titre de la page": dossier/nom-du-fichier.md
```

---

## 🌐 Déploiement Automatisé sur GitHub Pages

1. Reliez ce dossier local à votre dépôt GitHub et poussez vos fichiers :
   ```bash
   git init
   git add .
   git commit -m "feat: initialisation du portfolio technique MkDocs"
   git branch -M main
   git remote add origin https://github.com/chrskrgb/chrskrgb.github.io.git
   git push -u origin main
   ```
2. Sur GitHub, accédez à votre dépôt : **Settings** > **Pages** > **Build and deployment**.
3. Dans **Source**, sélectionnez **GitHub Actions**.
4. Le workflow `.github/workflows/deploy.yml` publiera automatiquement votre site sur **`https://chrskrgb.github.io/`** à chaque `git push` sur `main` !

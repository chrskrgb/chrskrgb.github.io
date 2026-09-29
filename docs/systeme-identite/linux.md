---
title: Administration & Hardening Linux
description: Procédures de durcissement et administration système sous distributions Debian / Ubuntu et RHEL
---

# Administration & Durcissement Linux

Cette documentation présente les standards de configuration minimale et de sécurisation appliqués sur les serveurs Linux en environnement de production.

---

## 1. Sécurisation du service SSH (`sshd_config`)

Fichier cible : `/etc/ssh/sshd_config.d/99-hardening.conf`

```ini title="Configuration SSH durcie"
# Désactivation de l'authentification par mot de passe
PasswordAuthentication no
ChallengeResponseAuthentication no

# Interdiction de connexion directe en root
PermitRootLogin no

# Restrictions sur les algorithmes cryptographiques modernes (Ed25519)
KexAlgorithms curve25519-sha256@libssh.org,diffie-hellman-group16-sha512
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com
MACs hmac-sha2-512-etm@openssh.com

# Timeout d'inactivité
ClientAliveInterval 300
ClientAliveCountMax 2
```

---

## 2. Configuration du pare-feu local (UFW / nftables)

```bash title="Commandes UFW de base"
# Politique par défaut restrictive
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Autorisation SSH sur port dédié (ex: 22)
sudo ufw allow 22/tcp comment 'SSH Management'

# Activation du pare-feu
sudo ufw enable
```

---

## 3. Supervision des accès et Fail2ban

Installation et paramétrage d'une prison `fail2ban` pour bannir automatiquement les adresses IP effectuant des tentatives de force brute sur le port SSH.

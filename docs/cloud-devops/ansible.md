---
title: Ansible - Automatisation & Configuration As Code
description: Architecture de rôles Ansible, inventaires et bonnes pratiques de configuration
---

# Automatisation & Gestion de Configuration avec Ansible

Ansible permet de standardiser et de maintenir l'état désiré des serveurs et équipements réseau de manière idempotente.

---

## 1. Structure recommandée pour un projet Ansible

```text
ansible-project/
├── ansible.cfg
├── inventory/
│   ├── production.ini
│   └── staging.ini
├── group_vars/
│   ├── all.yml
│   └── webservers.yml
├── roles/
│   ├── common/
│   ├── nginx/
│   └── security_hardening/
└── site.yml
```

---

## 2. Playbook exemple : Durcissement du socle commun

```yaml title="roles/common/tasks/main.yml"
---
- name: Mettre à jour le cache APT
  ansible.builtin.apt:
    update_cache: yes
    cache_valid_time: 3600
  when: ansible_os_family == "Debian"

- name: Installer les paquets de base essentiels
  ansible.builtin.package:
    name:
      - curl
      - htop
      - git
      - ufw
      - fail2ban
    state: present

- name: Activer et démarrer le service fail2ban
  ansible.builtin.service:
    name: fail2ban
    state: started
    enabled: yes
```

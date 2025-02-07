Role # Déploiement de MariaDB et Apache avec Ansible

## Description

Ce projet utilise **Ansible** pour automatiser l'installation, la configuration et le démarrage de **MariaDB** et **Apache** sur un ensemble de serveurs.  
L'approche repose sur la **modularité avec des rôles**, permettant une réutilisation facile dans d'autres projets.

---

## 📌 Prérequis

Avant d’exécuter ce projet, assurez-vous que :

- **Ansible** est installé sur votre machine de contrôle.
  ```bash
  sudo apt update && sudo apt install -y ansible
  ```
- Les machines cibles sont accessibles via **SSH**.
- Le fichier d’inventaire `/etc/ansible/hosts` est configuré avec les serveurs à gérer.
- Vous avez les droits **sudo** sur les machines cibles.

---

## 📌 Structure du projet

```
ansible-deployment/
├── apache/                # Rôle pour Apache
│   ├── tasks/main.yml
│   ├── handlers/main.yml
│   ├── templates/
│   ├── vars/main.yml
│   ├── meta/main.yml
│   └── README.md
│
├── mariadb/               # Rôle pour MariaDB
│   ├── tasks/main.yml
│   ├── handlers/main.yml
│   ├── vars/main.yml
│   ├── meta/main.yml
│   └── README.md
│
├── apache_mariadb.yml     # Playbook principal pour déployer Apache & MariaDB
├── test_playbook.yml      # Playbook pour tester les rôles individuellement
└── README.md              # Documentation du projet
```

---

## 📌 Variables du rôle MariaDB

Les variables suivantes sont utilisées dans **`mariadb/vars/main.yml`** :

```yaml
mariadb_root_password: "secure_password"
```

---

## 📌 Installation et configuration

### 👉 **Création des rôles**
Nous avons créé deux rôles pour structurer notre projet :

```bash
ansible-galaxy init apache
ansible-galaxy init mariadb
```

### 👉 **Déplacement des tâches dans les rôles**
Le fichier `tasks/main.yml` de chaque rôle contient l'installation et la configuration.

#### **Exemple pour Apache (`apache/tasks/main.yml`)**
```yaml
---
- name: Installer Apache
  apt:
    name: apache2
    state: present
    update_cache: yes

- name: Démarrer et activer Apache
  service:
    name: apache2
    state: started
    enabled: yes
```

#### **Exemple pour MariaDB (`mariadb/tasks/main.yml`)**
```yaml
---
- name: Installer MariaDB
  apt:
    name: mariadb-server
    state: present

- name: Démarrer et activer MariaDB
  service:
    name: mariadb
    state: started
    enabled: yes
```

---

## 📌 Exécution du playbook

### 👉 **Vérifier la syntaxe**
Avant d'exécuter le playbook, assurez-vous qu'il n'y a pas d'erreurs de syntaxe :
```bash
ansible-playbook apache_mariadb.yml --syntax-check
```

### 👉 **Exécution du playbook**
Lancez le playbook principal :
```bash
ansible-playbook  apache_mariadb.yml -k --ask-become-pass
```

### 👉 **Vérification des services**
Une fois l'exécution terminée, assurez-vous que les services sont bien installés et actifs :
```bash
sudo systemctl status apache2
sudo systemctl status mariadb
```

---

## 📌 Test et validation

Pour tester si les rôles sont réutilisables, créez un **nouveau playbook** et appliquez uniquement l'un des rôles :

```yaml
---
- name: Test du rôle Apache
  hosts: test_servers
  become: yes
  roles:
    - apache
```

Exécutez le test :
```bash
ansible-playbook test_playbook.yml -k --ask-become-pass
```

Si Apache s'installe et fonctionne correctement sur les nouveaux serveurs, cela confirme que les rôles sont **modulaires et réutilisables**. ✅

---

## 📌 Conclusion

- **Modularité** : Les rôles permettent une organisation claire et adaptable.
- **Réutilisabilité** : Les rôles peuvent être utilisés dans divers projets.
- **Automatisation efficace** : Une gestion simplifiée des installations et configurations.

Ce projet offre une solution robuste pour automatiser le déploiement de MariaDB et Apache avec Ansible.
# Déploiement Ansible : Grafana, Prometheus, Loki, Promtail

Ce projet Ansible permet de déployer automatiquement une stack de supervision complète sur un ou plusieurs serveurs Linux.

---

## Prérequis

- **Python** installé sur votre machine de contrôle
- **uv** installé 
- Accès SSH par clé aux serveurs cibles
- Python installé sur les cibles

---

## Mise en place de l’environnement avec uv

1. **Installez uv** (si ce n’est pas déjà fait) :
   ```sh
   pip install uv
   ```

2. **Créez et activez l’environnement virtuel avec uv** :
   ```sh
   uv venv ansible-venv
   source ansible-venv/bin/activate
   ```

3. **Installez les dépendances du projet** :
   ```sh
   uv pip install -r requirements.txt
   ```

---

## Structure du projet

```
ansible/
├── ansible.cfg
├── inventories/
│   └── inventory.yml
├── playbooks/
│   └── install_supervision.yml
├── roles/
│   ├── grafana/
│   ├── prometheus/
│   ├── loki/
│   └── promtail/
├── requirements.txt
└── README.md
```

---

## Configuration initiale

**Adaptez l’inventaire** dans `inventories/inventory.yml` selon vos hôtes et variables SSH.

---

## Utilisation

1. **Activez l’environnement virtuel** (si ce n’est pas déjà fait) :
   ```sh
   source ansible-venv/bin/activate
   ```

2. **Personnalisez les variables** dans `roles/<role>/defaults/main.yml`

3. **Lancez le playbook principal** :
   ```sh
   ansible-playbook playbooks/install_supervision.yml -K
   ```
---

## Exemple de playbook principal

```yaml
# playbooks/install_supervision.yml
- name: Install Grafana, Prometheus, Loki, and Promtail on remote servers
  hosts: all
  become: true
  gather_facts: true
  roles:
    - grafana
    - prometheus
    - loki
    - promtail
```

---

## Variables principales par rôle

---

### Prometheus (`roles/prometheus/defaults/main.yml`)
| Variable                        | Description                     | Valeur par défaut      |
|----------------------------------|---------------------------------|------------------------|
| `prometheus_dir_configuration`   | Dossier de configuration        | `/etc/prometheus`      |
| `prometheus_retention_time`      | Rétention des données           | `365d`                 |
| `prometheus_scrape_interval`     | Intervalle de scrutation global | `30s`                  |
| `prometheus_env`                 | Label d’environnement           | `production`           |
| `prometheus_var_config`          | Config complète                 | Voir fichier           |

---

### Loki (`roles/loki/defaults/main.yml`)
| Variable                  | Description                                 | Valeur par défaut      |
|---------------------------|---------------------------------------------|------------------------|
| `loki_version`            | Version à installer                         | `latest`               |
| `loki_http_listen_port`   | Port HTTP de Loki                           | `3100`                 |
| `loki_http_listen_address`| Adresse d’écoute                            | `0.0.0.0`              |
| `loki_expose_port`        | Ouvre le port Loki via firewalld            | `false`                |
| `loki_download_url_deb`   | URL du .deb Loki                            | Générée automatiquement|
| `loki_working_path`       | Dossier de travail                          | `/var/lib/loki`        |
| `loki_ruler_alert_path`   | Dossier des règles d’alerting               | `{{ loki_working_path }}/rules/fake` |
| `loki_auth_enabled`       | Authentification activée                    | `false`                |
| `loki_target`             | Cible Loki                                  | `all`                  |
| `loki_ballast_bytes`      | Ballast mémoire                             | `0`                    |
| `loki_server`             | Bloc server                                 | Voir fichier           |
| `loki_common`             | Bloc commun                                 | Voir fichier           |
| `loki_query_range`        | Bloc query_range                            | Voir fichier           |
| `loki_schema_config`      | Bloc schema_config                          | Voir fichier           |
| `loki_ruler`              | Bloc ruler                                  | Voir fichier           |
| `loki_analytics`          | Bloc analytics                              | Voir fichier           |

---

### Promtail (`roles/promtail/defaults/main.yml`)
| Variable                  | Description                                 | Valeur par défaut      |
|---------------------------|---------------------------------------------|------------------------|
| `promtail_version`        | Version à installer                         | `2.9.7`                |
| `promtail_bin_path`       | Chemin du binaire                           | `/usr/local/bin/promtail-linux-amd64` |
| `promtail_config_dir`     | Chemin du dossier de configuration                    | `/etc/promtail`        |
| `promtail_config_file`    | Chemin du fichier de configuration                    | `/etc/promtail/promtail-config.yaml` |
| `promtail_service_file`   | Chemin du fichier service systemd                     | `/etc/systemd/system/promtail.service` |
| `promtail_http_port`      | Port HTTP Promtail                          | `9080`                 |
| `promtail_grpc_port`      | Port gRPC Promtail                          | `0`                    |
| `promtail_positions_file` | Fichier de positions                        | `/tmp/positions.yaml`  |
| `promtail_loki_url`       | URL de Loki cible                           | `http://localhost:3100/loki/api/v1/push` |
| `promtail_job_name`       | Nom du job Promtail                         | `system`               |
| `promtail_job_label`      | Label du job Promtail                       | `varlogs`              |
| `promtail_log_path`       | Chemin des logs à collecter                 | `/var/log/*log`        |

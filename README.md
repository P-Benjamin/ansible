# Déploiement Ansible Grafana/Prometheus/Loki/Promtail

## Prérequis

- Ansible installé sur votre machine
- Accès SSH au serveur distant
- Une clé SSH générée et copiée sur le serveur distant

## Configuration

1. **Générez une clé SSH si besoin :**
   ```sh
   ssh-keygen -t ed25519 -C "votre_nom"
   ```

2. **Copiez la clé publique sur le serveur :**
   ```sh
   ssh-copy-id -i ~/.ssh/id_ed25519.pub utilisateur@ip_du_serveur
   ```

3. **Adaptez l’inventory :**
   Les variables suivantes peuvent être surchargées lors de l’exécution :
   - `remote_user` : utilisateur SSH distant (par défaut : benjamin)
   - `ssh_key_path` : chemin de la clé privée (par défaut : ~/.ssh/id_ed25519)

4. **Lancez le playbook :**
   ```sh
   ansible-playbook playbooks/install_grafna.yml -l remote
   ```

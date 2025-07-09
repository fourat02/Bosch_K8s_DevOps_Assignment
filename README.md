# Todo App K3s Deployment with Ansible Roles

Automated deployment of a Todo application on K3s using Ansible roles with SQLite storage and Traefik ingress.

## 🚀 Features

- **K3s cluster setup** with Traefik ingress
- **Todo Node.js app** with persistent SQLite storage
- **Modular Ansible roles** for infrastructure and application
- **Tagged execution** for selective deployment

## 📁 Project Structure

```
todo-k3s-ansible/
├── inventory.ini                    # Host config and variables
├── site.yml                        # Main playbook
├── ansible.cfg                     # Ansible configuration  
├── roles/
│   ├── k3s-setup/tasks/main.yml    # K3s setup tasks
│   └── todo-app/tasks/main.yml     # App deployment tasks
└── README.md
```

## ⚙️ Quick Setup

1. **Create structure**:
   ```bash
   mkdir -p roles/{k3s-setup,todo-app}/tasks
   ```

2. **Configure `inventory.ini`**:
   ```ini
   [myhosts]
   YOUR_HOST_IP ansible_user=YOUR_USER ansible_ssh_private_key_file=~/.ssh/id_rsa
   
   [myhosts:vars]
   app_namespace=todo-app
   todo_app_hostname=todo.local
   todo_app_image=fourat02/getting-started:latest
   # ... other variables
   ```

3. **Deploy**:
   ```bash
   ansible-playbook site.yml --ask-become-pass
   ```

4. **Add to `/etc/hosts`**:
   ```
   YOUR_HOST_IP todo.local
   ```

5. **Access**: `http://todo.local`

## 📚 Usage Commands

```bash
# Complete deployment
ansible-playbook site.yml --ask-become-pass

# Infrastructure only (K3s setup)
ansible-playbook site.yml --ask-become-pass --tags k3s

# Application only
ansible-playbook site.yml --ask-become-pass --tags app

# List available tags
ansible-playbook site.yml --list-tags
```

## 🏗️ Roles & Tags

| Role | Tags | Purpose |
|------|------|---------|
| `k3s-setup` | `k3s`, `infrastructure` | Install K3s, Traefik CRDs |
| `todo-app` | `app`, `deployment` | Deploy Todo app, storage, ingress |

## 🗂️ Storage

- **SQLite data**: Stored at `/opt/todo-data` on host (persistent)
- **To reset data**: `sudo rm -rf /opt/todo-data` and redeploy

## 🔍 Troubleshooting

```bash
# Check K3s
sudo systemctl status k3s
sudo kubectl get nodes

# Check app
sudo kubectl get all -n todo-app
sudo kubectl logs -n todo-app deployment/todo-app

# Check tags
ansible-playbook site.yml --list-tags

# Verbose output
ansible-playbook site.yml --ask-become-pass -v
```

## 🧹 Cleanup

```bash
# Remove app
kubectl delete namespace todo-app

# Remove data
sudo rm -rf /opt/todo-data

# Uninstall K3s
sudo /usr/local/bin/k3s-uninstall.sh
```
# Todo App K3s Deployment with Ansible

This project automates the deployment of a Todo application on a K3s Kubernetes cluster using Ansible. The deployment includes K3s setup, Traefik ingress controller, and a Node.js Todo application with SQLite storage and optional MySQL fallback.

## 🚀 Features

- **Automated K3s Installation**: Single-node Kubernetes cluster setup
- **Traefik Ingress**: HTTP routing with security headers middleware
- **Persistent Storage**: SQLite database with local persistent volumes
- **MySQL Fallback**: Optional MySQL database support
- **Security Headers**: XSS protection, frame denial, and content type validation
- **Resource Management**: CPU and memory limits for optimal performance
- **Modular Playbooks**: Separate infrastructure and application deployment

## 📋 Prerequisites

### Control Machine (where you run Ansible)
- Ansible 2.9+
- Python 3.6+
- SSH access to target host

### Target Host Requirements
- Ubuntu/Debian-based system
- SSH access with sudo privileges
- Internet connectivity
- Minimum 2GB RAM, 2 CPU cores
- 20GB+ available disk space

## 📁 Project Structure

```
todo-k3s-ansible/
├── inventory.ini           # Hosts and configuration variables
├── site.yml               # Main playbook (runs both setup and deployment)
├── k3s-setup.yml          # K3s cluster and Traefik CRD setup
├── app-deployment.yml     # Todo application deployment
└── README.md              # This file
```

## ⚙️ Configuration

All configuration is managed through the `inventory.ini` file:

### Host Configuration
```ini
[myhosts]
10.211.55.14 ansible_user=parallels ansible_ssh_private_key_file=~/.ssh/id_rsa ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

### Application Variables
| Variable | Default | Description |
|----------|---------|-------------|
| `app_namespace` | `todo-app` | Kubernetes namespace for the application |
| `app_replicas` | `1` | Number of application replicas |
| `todo_app_port` | `3000` | Port the Todo app listens on |
| `todo_app_image` | `fourat02/getting-started:latest` | Docker image for the Todo app |
| `todo_app_hostname` | `todo.local` | Hostname for accessing the application |
| `node_env` | `production` | Node.js environment |
| `db_type` | `SQLITE` | Database type (SQLITE or MYSQL) |

### MySQL Fallback (Optional)
| Variable | Default | Description |
|----------|---------|-------------|
| `enable_mysql_fallback` | `False` | Enable MySQL instead of SQLite |
| `mysql_database` | `todoapp` | MySQL database name |
| `mysql_user` | `todouser` | MySQL username |
| `mysql_password` | `todopass123` | MySQL password |
| `mysql_root_password` | `rootpass123` | MySQL root password |

## 🚀 Quick Start

### 1. Clone and Setup
```bash
git clone <your-repo-url>
cd todo-k3s-ansible
```

### 2. Configure Inventory
Edit `inventory.ini` with your target host details:
```ini
[myhosts]
YOUR_HOST_IP ansible_user=YOUR_USER ansible_ssh_private_key_file=~/.ssh/id_rsa
```

### 3. Deploy Everything
```bash
ansible-playbook -i inventory.ini site.yml
```

### 4. Add Host Entry
Add this line to your `/etc/hosts` file:
```
YOUR_HOST_IP todo.local
```

### 5. Access Application
Open your browser and navigate to: `http://todo.local`

## 📚 Usage Options

### Complete Deployment (Recommended)
Runs K3s setup, waits 30 seconds for stabilization, then deploys the application:
```bash
ansible-playbook -i inventory.ini site.yml
```

### Infrastructure Only
Sets up K3s cluster and Traefik CRDs:
```bash
ansible-playbook -i inventory.ini k3s-setup.yml
```

### Application Only
Deploys the Todo app (requires existing K3s cluster):
```bash
ansible-playbook -i inventory.ini app-deployment.yml
```

## 🔧 What Gets Deployed

### Infrastructure Components
- **K3s Kubernetes Cluster**: Lightweight Kubernetes distribution
- **Traefik Ingress Controller**: Built-in with K3s
- **Traefik CRDs**: Custom resource definitions for advanced routing
- **Local Storage**: Persistent volume for SQLite database

### Application Components
- **Todo App Deployment**: Node.js application with resource limits
- **SQLite Database**: Persistent storage on local filesystem
- **MySQL (Optional)**: Alternative database backend
- **Kubernetes Services**: ClusterIP services for internal communication
- **Ingress Routes**: HTTP routing with security middleware
- **ConfigMaps & Secrets**: Configuration and sensitive data management

### Security Features
- **Security Headers**: XSS protection, frame denial, content type validation
- **Resource Limits**: CPU and memory constraints
- **Network Policies**: Service-to-service communication control

## 🗂️ Storage

### SQLite (Default)
- **Location**: `/opt/todo-data` on the host
- **Mount Point**: `/etc/todos` in the container
- **Database File**: `todo.db`
- **Persistent Volume**: 512Mi local storage

### MySQL (Optional)
- **In-cluster MySQL**: Deployed as a separate pod
- **Credentials**: Stored in Kubernetes secrets
- **Resource Limits**: 512Mi memory, 500m CPU

## 🌐 Networking

- **Application Port**: 3000 (internal)
- **Service Port**: 3000 (ClusterIP)
- **Ingress**: HTTP on port 80
- **Hostname**: Configurable via `todo_app_hostname`

## 🔍 Troubleshooting

### Check K3s Status
```bash
sudo systemctl status k3s
sudo kubectl get nodes
```

### Check Application Status
```bash
sudo kubectl get pods -n todo-app
sudo kubectl get services -n todo-app
sudo kubectl get ingress -n todo-app
```

### View Application Logs
```bash
sudo kubectl logs -n todo-app deployment/todo-app
```

### Check Traefik
```bash
sudo kubectl get pods -n kube-system | grep traefik
```

### Common Issues

1. **Can't access the application**
   - Verify `/etc/hosts` entry
   - Check if K3s is running: `sudo systemctl status k3s`
   - Verify ingress routes: `sudo kubectl get ingressroute -n todo-app`

2. **Database issues**
   - Check persistent volume: `sudo kubectl get pv,pvc -n todo-app`
   - Verify storage directory: `ls -la /opt/todo-data`

3. **Pod not starting**
   - Check resource constraints
   - Verify image availability
   - Review pod events: `sudo kubectl describe pod -n todo-app`

## 🔄 Updating the Application

To update the application image:
1. Update `todo_app_image` in `inventory.ini`
2. Run: `ansible-playbook -i inventory.ini app-deployment.yml`

## 🧹 Cleanup

To remove the entire deployment:
```bash
# Remove application
sudo kubectl delete namespace todo-app

# Uninstall K3s
sudo /usr/local/bin/k3s-uninstall.sh

# Remove data directory
sudo rm -rf /opt/todo-data
```

## 📝 License

This project is licensed under the MIT License.

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📞 Support

For issues and questions:
- Check the troubleshooting section
- Review Kubernetes events and logs
- Verify network connectivity and DNS resolution
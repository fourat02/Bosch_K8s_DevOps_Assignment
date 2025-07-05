# Bosch DevOps Kubernetes Assignment

This repository contains the solution for the Bosch Software DevOps Internship challenge.

## Structure

- `ansible/`: Ansible playbook to set up Docker, Kubernetes, Helm, and deploy app
- `k8s/`: Kubernetes manifests for the sample Todo application (frontend + MySQL)
- `docs/`: Supplementary documentation and screenshots

## Steps

1. Run the Ansible playbook to configure the VM and initialize the Kubernetes cluster.
2. Use Helm to install Ingress Controller.
3. Apply Kubernetes manifests for the Todo app.
4. Access the app via Ingress or NodePort (see below).

## Requirements

- Ubuntu VM with SSH access
- Ansible installed on the control node
- Internet access for package installations

## Author

Fourat Ben Abdeljelil

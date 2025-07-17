# Ansible Playbook to deploy Solana Monitoring Tools

This Ansible playbook automates the deployment and configuration of Solana Monitoring Tools. It ensures that the necessary dependencies, configuration files, and services are properly set up and running.

## Table of Contents

- [Requirements](#requirements)
- [Setup](#setup)
- [Variables](#variables)
- [Usage](#usage)

## Requirements

Before using this playbook, ensure the following requirements are met:

1. **Ansible version**: Make sure you have Ansible 2.15+ installed.
2. **SSH Access**: Passwordless SSH access to all target servers.
3. **Python**: Python 3.x installed on the control node and all target hosts.
4. **Privileges**: The user running the playbook must have sudo privileges on the target machines.

## Setup

### 1. Install Ansible

If Ansible is not installed, visit the official documentation for detailed instructions on how to install Ansible on various Linux distributions:

[Ansible Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html)

### 2. Clone the repository

Clone this repository to your Ansible control node:

```bash
git clone https://github.com/encapsulate-xyz/solana-monitoring-ops.git
cd solana-monitoring-ops
```

### 3. Inventory

Define your target servers' IP address or DNS in the inventory folder, and select `inventory.yml` to update.

```yaml
---
all:
  children:
    monitor:
      hosts:
        validator.solana.mainnet.encapsulate.xyz:
```

## Variables

This playbook allows customization through several variables. You can define these variables in the following locations:

- **`group_vars/all.yml`**: Contains all the source urls and configurations.

# 📘 Configuration Reference

## 🔧 Global Settings

| Variable         | Default Value                     | Description |
|------------------|-----------------------------------|-------------|
| `org`            | `encapsulate`                     | Organization name prefix. |
| `tmp_dir`        | `/tmp`                            | Temporary directory for downloads or extraction. |
| `domain_regex`   | `^([a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}$` | Regex for domain validation. |
| `ansible_port`   | `8192`                            | Default SSH port used by Ansible. |


## 🔔 Alert Manager

| Variable                                  | Default / Example                      | Description |
|-------------------------------------------|----------------------------------------|-------------|
| `alert_manager.service_identifier`        | `alert-manager`                        | Internal service name. |
| `alert_manager.source_url`                | `https://github.com/prometheus/alertmanager/releases/download` | Source URL for binary. |
| `alert_manager.file_extension`            | `tar.gz`                               | File format. |
| `alert_manager.ports.web_external_port`   | `9093`                                 | Web access port. |
| `alert_manager.smtp_smarthost`            | `smtp.gmail.com:587`                   | SMTP relay host. |
| `alert_manager.smtp_sender`               | *(set this)*                           | Email to send alerts from. |
| `alert_manager.smtp_receiver`             | *(set this)*                           | Email to receive alerts. |


## 📊 Grafana

| Variable                         | Default / Example                      | Description |
|----------------------------------|----------------------------------------|-------------|
| `grafana.service_identifier`     | `grafana`                              | Internal identifier. |
| `grafana.source_url`             | `https://dl.grafana.com/oss/release`   | Binary source URL. |
| `grafana.file_extension`         | `tar.gz`                               | File type. |
| `grafana.admin_user`             | `{{ org }}-grafana`                    | Admin user login. |
| `grafana.admin_email`            | *(set this)*                           | Admin email for Grafana login. |
| `grafana.ports.http_port`        | `3000`                                 | Web interface port. |


## 📦 Loki

| Variable                         | Default / Example                            | Description |
|----------------------------------|----------------------------------------------|-------------|
| `loki.service_identifier`        | `loki`                                       | Service name. |
| `loki.source_url`                | `https://github.com/grafana/loki/releases/download` | Download URL. |
| `loki.file_extension`            | `zip`                                        | File format. |
| `loki.ports.http_listen_port`    | `3100`                                       | HTTP port. |
| `loki.ports.grpc_listen_port`    | `3101`                                       | gRPC port. |


## 📈 Prometheus

| Variable                               | Default / Example                            | Description |
|----------------------------------------|----------------------------------------------|-------------|
| `prometheus.service_identifier`        | `prometheus`                                 | Internal tag. |
| `prometheus.source_url`                | `https://github.com/prometheus/prometheus/releases/download` | Source URL. |
| `prometheus.file_extension`            | `tar.gz`                                     | Format. |
| `prometheus.ports.web_listen_address`  | `9090`                                       | Web UI port. |


## 🌐 Node Exporter

| Variable                                 | Default / Example                            | Description |
|------------------------------------------|----------------------------------------------|-------------|
| `node_exporter.service_identifier`       | `node-exporter`                              | Identifier. |
| `node_exporter.source_url`               | `https://github.com/prometheus/node_exporter/releases/download` | Download URL. |
| `node_exporter.file_extension`           | `tar.gz`                                     | Archive format. |
| `node_exporter.ports.web_listen_address` | `9100`                                       | Metrics port. |


## 🪵 Promtail

| Variable                              | Default / Example                            | Description |
|---------------------------------------|----------------------------------------------|-------------|
| `promtail.service_identifier`         | `promtail`                                   | Service ID. |
| `promtail.source_url`                 | `https://github.com/grafana/loki/releases/download` | Binary URL. |
| `promtail.file_extension`             | `zip`                                        | Format. |
| `promtail.ports.http_listen_port`     | `9080`                                       | HTTP metrics port. |
| `promtail.ports.grpc_listen_port`     | `9081`                                       | gRPC port. |


## ⚙️ Solana Exporter

| Variable                                 | Default / Example                            | Description |
|------------------------------------------|----------------------------------------------|-------------|
| `solana_exporter.service_identifier`     | `solana-exporter`                            | Service ID. |
| `solana_exporter.source_url`             | `https://github.com/qskyhigh/solana-monitor-public` | GitHub source. |
| `solana_exporter.ports.metric_port`      | `1234`                                       | Exported metrics port. |
| `solana_exporter.validator_rpc_endpoint` | `http://localhost:8899`                      | Validator RPC endpoint. |
| `solana_exporter.network_rpc_endpoint`   | `https://api.testnet.solana.com`             | External RPC endpoint. |
| `solana_exporter.pub_key`                | *(set this)*                                 | Validator public key. |
| `solana_exporter.vote_pub_key`           | *(set this)*                                 | Vote account public key. |
| `solana_exporter.solana_binary_path`     | `/opt/solana/bin/solana-cli`                 | Path to Solana CLI binary. |

- **`group_vars/default.yml`**: Contains service version variables.

- **`group_vars/vault.yml`**: Store secret variables, such as `jwtsecret`, in this file.
# 🔐 Vault Configuration Reference

The following variables are used for secure authentication, alerting, and external access management across Prometheus stack components.

## 🧑 Default Auth

| Variable                        | Default / Example                              | Description |
|--------------------------------|--------------------------------------------------|-------------|
| `vault.default.auth_user`      | `encapsulate`                                   | Default username for basic auth access. |
| `vault.default.auth_password`  | *(set this)*                    | Encrypted password (bcrypt hash) for the default user. |


## 🚨 PagerDuty Routing Keys

| Variable                                      | Example / Key                          | Description |
|-----------------------------------------------|-----------------------------------------|-------------|
| `vault.default.pagerduty.default_routing_key` | *(set this)*         | Primary routing key for PagerDuty alerts. |
| `vault.default.pagerduty.node_exporter_routing_key` | *(set this)*   | Routing key for Node Exporter alerts. |
| `vault.default.pagerduty.low_urgency_routing_key` | *(set this)*           | Routing key for low urgency alerts. |
| `vault.default.pagerduty.solana_routing_key`  | *(set this)*              | Routing key for Solana-related alerts. |


## 🔒 Prometheus Credentials

| Variable                                         | Example        | Description |
|--------------------------------------------------|----------------|-------------|
| `vault.prometheus.alert_manager_auth_password`   | *(set this)*| Password for basic auth in Prometheus AlertManager. |


## 🔒 Loki Credentials

| Variable                                     | Example         | Description |
|----------------------------------------------|-----------------|-------------|
| `vault.loki.alert_manager_auth_password`     | *(set this)* | Password for Loki’s AlertManager (if shared). |


## 📊 Grafana Secrets

| Variable                        | Example        | Description |
|----------------------------------|----------------|-------------|
| `vault.grafana.admin_password`   | *(set this)*| Admin login password for Grafana. |
| `vault.grafana.secret_key`       | *(set this)* | Secret key for session encryption in Grafana. |


## 🌐 External Prometheus Access

| Variable                                     | Example / Value                                                             | Description |
|----------------------------------------------|------------------------------------------------------------------------------|-------------|
| `vault.external.prometheus.password`         | *(set this)*                                     | External access password. |


### Usage

1. First, install the dependencies:

  ```bash
   ansible-galaxy install -r collections/requirements.yml
  ```

2. Create a `ansible_vault_password` file containing ansible-vault password

3. Then run the playbook:

  ```bash
  ansible-playbook main.yml -i inventory/inventory.yml
  ```

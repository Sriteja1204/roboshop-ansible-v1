# RoboShop - Ansible Automation

Ansible playbooks and roles to provision and configure the **RoboShop** e-commerce application infrastructure on AWS — built as part of a hands-on DevOps learning path (manual deployment first, then automation).

## About RoboShop

RoboShop is an 11-tier e-commerce application:

| Component | Description |
|---|---|
| MongoDB | NoSQL database |
| Redis | In-memory cache |
| MySQL | Relational database |
| RabbitMQ | Message broker |
| Catalogue | Product catalogue service |
| User | User management service |
| Cart | Shopping cart service |
| Shipping | Shipping calculation service |
| Payment | Payment processing service |
| Dispatch | Order dispatch service |
| Frontend | Nginx reverse proxy / web UI |

Each component runs on its own AWS EC2 instance (RHEL 9), with its own security group.

## Project Structure

```
roboshop-ansible/
├── group_vars/             # Variables scoped per host group (per service)
├── roles/                  # Ansible roles — one per service (tasks, templates, handlers)
├── ansible.cfg              # Ansible configuration (inventory path, SSH settings, etc.)
├── common.yaml              # Shared/common setup tasks across all services
├── instance-creation.yaml   # Provisions EC2 instances + Route53 DNS records
├── inventory.ini            # Static inventory of target hosts
└── roboshop.yaml            # Main playbook — configures a service by component name
```

## Prerequisites

- Ansible installed on the control machine
- AWS CLI configured with valid credentials
- `amazon.aws` Ansible collection installed:
  ```bash
  ansible-galaxy collection install amazon.aws
  ```
- An existing AWS account with a VPC/subnets available
- Route 53 hosted zone for DNS record management

## Usage

### 1. Provision EC2 instances and DNS records

```bash
ansible-playbook instance-creation.yaml -e "action=create"
```

To tear down infrastructure:
```bash
ansible-playbook instance-creation.yaml -e "action=delete"
```

### 2. Configure a specific service

```bash
ansible-playbook roboshop.yaml -e "component=mongodb"
```
Replace `mongodb` with any service name (`redis`, `mysql`, `rabbitmq`, `catalogue`, `user`, `cart`, `shipping`, `payment`, `dispatch`, `frontend`).

### 3. Run common setup tasks

```bash
ansible-playbook common.yaml
```

## Configuration

- **`inventory.ini`** — defines host groups per service; update with your instance IPs/DNS names.
- **`group_vars/`** — holds variables specific to each service group (ports, config values, etc.).
- **`ansible.cfg`** — pre-configured with inventory path, remote user, and SSH settings for this project.

## Secrets Management

Sensitive values (database passwords, API keys) are not committed in plaintext. This project uses [Ansible Vault / AWS Secrets Manager — *update based on what you're using*] for secrets.

## Notes

This project follows a **manual-first, automate-second** learning approach — each service was deployed manually before being converted into an Ansible role, to build a solid understanding of what the automation is actually doing.

## Author

Sri Teja

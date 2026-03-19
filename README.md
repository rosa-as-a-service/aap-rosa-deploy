# aap-rosa-deploy

Repository that hosts all the Ansible Automation Platform components, required to deploy ROSA HCP clusters using the [`rh.rosa`](https://github.com/rosa-as-a-service/rh.rosa) collection.

Spoke clusters are provisioned as a self-service offering through the [AAP API](https://docs.ansible.com/automation-controller/latest/html/controllerapi/index.html) or the [Self-Service Automation Portal](https://www.redhat.com/en/technologies/management/ansible/self-service-automation). The hub cluster is typically deployed once via the CLI.

## Setup

### Standalone

```bash
ansible-galaxy collection install -r collections/requirements.yml
python3 -m pip install -r build/requirements.txt
```

### AAP

1. Run the `configure_aap.yaml` playbook to import the jobs, workflows, and credential types
2. Update each of the credentials in AAP that the previous step created

## Configuration

### Variables

Configure cluster settings in `group_vars/all/vars.yaml`:

```yaml
rosa_version: "4.18.0"
rosa_admin_username: "cluster-admin"

# Hub
rosa_hub_cluster_name: hub
rosa_hub_worker_nodes: 2
rosa_hub_worker_instance_type: m5.4xlarge
rosa_hub_subnets:
  - name: "hub-private-2a"
    id: "subnet-0123456789abcdef0"

# Spoke
rosa_spoke_cluster_name: spoke1
rosa_spoke_worker_nodes: 2
rosa_spoke_worker_instance_type: m5.2xlarge
rosa_spoke_subnets:
  - name: "spoke1-rosa-2a"
    id: "subnet-0123456789abcdef1"
```

### Secrets

The following variables should be stored in an Ansible Vault encrypted file (`group_vars/all/secrets.yaml`):

| Variable | Description |
|---|---|
| `rosa_token` | ROSA / Red Hat API token |
| `rosa_admin_password` | Cluster admin password |
| `aws_hub_account_id` | AWS account ID for the hub cluster |
| `aws_hub_access_key_id` | AWS access key ID for the hub account |
| `aws_hub_secret_access_key` | AWS secret access key for the hub account |
| `aws_spoke_account_id` | AWS account ID for the spoke cluster |
| `aws_spoke_access_key_id` | AWS access key ID for the spoke account |
| `aws_spoke_secret_access_key` | AWS secret access key for the spoke account |

## Use

### Deploy Hub (Standalone)

The hub cluster is deployed once from the CLI:

```bash
ansible-playbook rh.rosa.deploy_hub_rosa -v --vault-id @prompt
```

### Spoke Clusters (AAP)

Spoke clusters are provisioned and destroyed through AAP. Users submit requests via the AAP API or the Self-Service Automation Portal, which triggers the pre-configured workflows.

The `org.json` file contains the AAP job templates and workflows:

| Workflow | Description | Steps |
|---|---|---|
| **Deploy Spoke ROSA** | Provisions a new spoke cluster | Preflight → Deploy → Bootstrap |
| **Destroy Spoke ROSA** | Tears down a spoke cluster | Destroy |

The **Deploy Spoke ROSA** workflow survey prompts the user for:
- AWS Access Key ID
- AWS Secret Access Key
- ROSA / Red Hat API token

Cluster configuration (name, instance type, worker count, subnets) is passed as extra variables at launch time.

### Standalone Spoke (Development)

For local development and testing, spoke clusters can also be managed from the CLI:

```bash
# Deploy
ansible-playbook rh.rosa.deploy_spoke_rosa -v --vault-id @prompt

# Destroy
ansible-playbook spoke_destroy.yml -v --vault-id @prompt
```

## Private Clusters (Bastion)

If using private subnets, you will need a bastion/jump host.

1. Add the bastion host to `inventory.yaml`:

    ```yaml
    all:
      hosts:
        bastion-spoke1:
          ansible_host: <bastion-spoke-ip>
          ansible_user: ec2-user
          ansible_ssh_private_key_file: "~/.ssh/bastion.pem"
    ```

2. Set the `spoke_bastion` variable in `group_vars/all/vars.yaml`:

    ```yaml
    spoke_bastion: bastion-spoke1
    ```

3. Use the bastion-aware playbooks:

    ```bash
    # Deploy
    ansible-playbook rh.rosa.deploy_spoke_rosa_with_bastion -v --vault-id @prompt

    # Destroy
    ansible-playbook spoke_destroy_with_bastion.yml -v --vault-id @prompt
    ```

## Testing

### Molecule

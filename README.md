# aap-rosa-deploy
Repository that hosts all the Ansible Automation Platform components, required to deploy ROSA

> **Note:**
>
> If using Private subnets, you **will** need a bastion/jump host.
>
> That will mean updating the inventory file to include the bastion host details, and using the `rh.rosa.deploy_spoke_rosa_with_bastion` playbook - ensuring that the `spoke_bastion` variable is defined and is equal to the name of the bastion host in the inventory
>
> EG:
> ```yaml
> # group_vars/all/vars.yaml
> spoke_bastion: bastion-spoke1
> ```
> ```yaml
> # inventory.yaml
> all:
>   hosts:
>     bastion-spoke1:
>       ansible_host: <bastion-spoke-ip>
>       ansible_user: ec2-user
>       ansible_ssh_private_key_file: "~/.ssh/bastion.pem"
> ```

## Setup

### Standalone

```bash
ansible-galaxy collection install -r collections/requirements.yml
python3 -m pip install -r build/requirements.txt
```

### AAP

1. Run the `configure_aap.yaml` playbook to import the jobs, workflows, credential types
2. Update each of the credentials in AAP that the previous step created

### Prerequisites

### Configuration

## Use

### Standalone

#### Deploy Hub

```bash
ansible-playbook rh.rosa.deploy_hub_rosa -v --vault-id @prompt
```

#### Deploy Spoke

```bash
ansible-playbook rh.rosa.deploy_spoke_rosa -v --vault-id @prompt
```

### Ansible Automation Platform

## Testing

### Molecule
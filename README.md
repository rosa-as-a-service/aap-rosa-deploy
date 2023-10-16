# aap-rosa-deploy
Repository that hosts all the Ansible Automation Platform components, required to deploy ROSA

> **Note:**
>
> If using Private subnets, you **will** need a bastion/jump host.
>
> For standalone, that will mean updating the inventory file to include the bastion host details.
> For AAP, that will mean deploy an Execution Node and specifying that for `bootstrap_X` plays.

## Setup

### Standalone

```bash
ansible-galaxy collection install -r collections/requirements.yml
python3 -m pip install -r build/requirements.txt
```

### AAP

1. Create credentials
2. Create job template
3. Create workflow using job templates
4. Ensure correct custome Execution Environment is used
    1. quay.io/ahussey/aap-rosa-ee

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
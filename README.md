# aap-rosa-deploy
Repository that hosts all the Ansible Automation Platform components, required to deploy ROSA

## Setup

### Standalone

```bash
ansible-galaxy collection install -r collections/requirements.yml
python3 -m pip install -r requirements.txt
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
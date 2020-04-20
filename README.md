# Ansible-tower-cloud

Playbooks and guides for enabling a a redundant website in Azure, AWS and Oracle Cloud using Ansible Tower

All playbooks are for ansible tower, for Ansible command line see [https://github.com/jesperberth/ansible-cloud](https://github.com/jesperberth/ansible-cloud)

## Oracle Cloud

### Prepare Ansible Tower for Oracle Cloud

#### Install Python OCI and OCI ansible modules

```bash
ssh user@ansibletowerhost

source /var/lib/awx/venv/ansible/bin/activate

which python

sudo yum install python-pip

sudo pip install oci

sudo yum install git

git clone https://github.com/oracle/oci-ansible-modules.git

cd oci-ansible-modules/

sudo ./install.py

ansible --version

ansible-doc oci_api_key

deactivate
```

#### Create a new Credential Type in Ansible Tower

Log in to ansible tower as an admin

Administration / Credential Types

Add new

Set Name: Oracle Cloud

Input Configuration

```bash
fields:
  - id: api_user
    type: string
    label: User
  - id: api_user_fingerprint
    type: string
    label: fingerprint
  - id: api_user_key_file
    type: string
    label: Key File
    secret: true
    multiline: true
  - id: api_user_key_pass_phrase
    type: string
    label: Pass Phrase
    secret: true
  - id: tenancy
    type: string
    label: Tenancy
  - id: region
    type: string
    label: Region
  - id: config_file
    type: string
    label: Config File
    multiline: true
required:
  - api_user
  - api_user_fingerprint
  - api_user_key_file
  - tenancy
  - region
  - config_file
```

Injector Configuration

```bash
env:
  OCI_CONFIG_FILE: '{{ tower.filename.configfile }}'
  OCI_REGION: '{{ region }}'
  OCI_TENANCY: '{{ tenancy }}'
  OCI_USER_FINGERPRINT: '{{ api_user_fingerprint }}'
  OCI_USER_ID: '{{ api_user }}'
  OCI_USER_KEY_FILE: '{{ tower.filename.keyfile }}'
  OCI_USER_KEY_PASS_PHRASE: '{{ api_user_key_pass_phrase }}'
file:
  template.configfile: '{{ config_file }}'
  template.keyfile: '{{ api_user_key_file }}'
  ```

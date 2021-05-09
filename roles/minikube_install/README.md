# redhat_cop.tower_utilities.minikube_install

Ansible role to install Ansible Tower on Minikube.

## Requirements

python >= 2.7

---PYTHON MODULES---
* openshift
* kubernetes
* PyYAML >= 3.11
* requests-oauthlib

# minikube Requirements
minikube v1.18.1

minikube default resource requirements:
* 6GB RAM,
* 2CPU cores

A setup and running minikube

context set for namespace

## Role Variables

```yaml

# The following parameters must be set to ensure a successful deployment

# Directory from which Tower installation will launch
tower_working_location: "/var/tmp"

# Location of tower version to install
tower_ocp_releases_url: https://releases.ansible.com/ansible-tower/setup_openshift/
tower_ocp_setup_file: ansible-tower-openshift-setup-{{ tower_release_version }}.tar.gz

# This will create or update a default admin (superuser) account in Tower
admin_user: 'admin'
admin_password: 'password'

# Tower Secret key
# It's *very* important that this stay the same between upgrades or you will lose
# the ability to decrypt your credentials
secret_key: 'mysecretkey'

# Database Settings
# =================

# Set pg_hostname if you have an external postgres server, otherwise
# a new postgres service will be created
# pg_hostname=postgresql

# If using an external database, provide your existing credentials.
# If you choose to use the provided containerized Postgres depolyment, these
# values will be used when provisioning the database.
pg_username: 'awx'
pg_password: 'awx'
pg_database: 'tower'
pg_port: 5432
pg_sslmode: 'prefer'  # set to 'verify-full' for client-side enforced SSL

# Note: The user running this installer will need cluster-admin privileges.
# Tower's job execution container requires running in privileged mode,
# and a service account must be created for auto peer-discovery to work.

# Optional containerised Postgres DB settings
# =============================
# Skip this section if you BYO database. This is only used when you want the
# installer to deploy a containerized Postgres deployment inside of your
# OpenShift cluster. This is only recommended if you have experience storing and
# managing persistent data in containerized environments.
#
# Choose a name for the pg persistant volume claim to be created:
openshift_pg_pvc_name: postgresql

# Deploy into Vanilla Kubernetes
# ==============================
# Tower can be deployed to a Kubernetes instance for testing purposes.
# A context should be set.
# A pull secret for registry.redhat.io is likely needed  if not already setup.

kubernetes_context: minikube
kubernetes_namespace: default
kubernetes_image_pull_secrets: tower-mini-pull-secret
```

## Example Playbook

The following playbook and accompanying vars file containing the defined seed objects above, can be invoked in the following manner. It is best practice to give the password at runtime to ensure the password is not saved in the inventory.

Before running the playbook, you will first need to install the required collection.

```sh
$ ansible-galaxy collection install redhat_cop.tower_utilities
```

The playbook should be run in one of the following ways, dependant upon if you are using a token or password to access the openshift cluster

```sh
$ ansible-playbook playbook.yml -e @tower_vars.yml -e openshift_password=password
```
```sh
$ ansible-playbook playbook.yml -e @tower_vars.yml -e openshift_token=example-token
```

```yaml
---
# Playbook to install Ansible Tower as a single node

- name: Install Ansible Tower on OCP
  hosts: localhost
  become: true
  vars:
    tower_release_version: latest
  roles:
    - redhat_cop.tower_utilities.install_minikube
```

## License

MIT

## Author Information

Chris Renwick
Sean Sullivan
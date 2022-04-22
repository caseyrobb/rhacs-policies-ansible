# Red Hat Advanced Cluster Security policy management with Ansible

This repo contains 2 playbooks that:
1. Retrieve all user-created policies from an ACS Central instance and write them to individual YAML files
2. Combine the individual policy YAMLs into a single JSON payload which is then used to create a `configMap` object

# Prerequisites
1. Ansible 2.9
2. OpenShift `oc` client binary in $PATH
3. API token to RHACS Central with Admin privileges

## Configuring the Ansible vars
The vars file `vars/vault.yml` contains two vaulted variables:
- `vaulted_acs_host` - the URL of the Central instance
- `vaulted_acs_token` - an API token with Admin privs

Values for each variable can be created using the `ansible-vault` utility:
```
ansible-vault --ask-vault-pass encrypt_string central-acs.apps.mycluster.example.com
```

## Fetching user-created policies from RHACS
User defined policies can be retrieved with the `fetch-policies.yml` playbook, which then write the individual policies to `files/current_policies`
```
ansible-playbook --ask-vault-pass fetch-policies.yml
```

## Creating a policy configMap
The `create-policy-cm.yml` playbook merges the YAML policies in `files/current_policies` and writes an configMap manifest to `files/policies-cm.yaml`.  An example Job using the generated `configMap` can be found in my [rhacs-config-gitops repo](https://github.com/caseyrobb/rhacs-config-gitops/blob/master/manifests/policy/policy-config-job.yaml).

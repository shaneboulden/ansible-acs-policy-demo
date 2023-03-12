# Ansible and ACS Policy-as-Code demo

A demonstration showcasing GitOps policy-as-code approaches with Red Hat Advanced Cluster Security for Kubernetes (ACS) and Ansible.

## Preparing the demo environment
Fork this repo to your own account.

### ACS
Create a new 'StackRox API integration' admin token in Red Hat Advanced Cluster Security for Kubernetes (ACS) and copy the value.

![StackRox API token 1](/docs-images/stackrox-api-token-1.png)

![StackRox API token 2](/docs-images/stackrox-api-token-2.png)

Update the Ansible `vault.yml` to reference your ACS cluster and the API token.
```yaml
vaulted_acs_host: central-acs-central.apps.cluster.example.com:443
vaulted_acs_token: your-acs-admin-token
```

### Ansible
Create the components in Ansible Automation Platform required to orchestrate policy updates.

#### Project
Create a new project, and replace the URL with your forked copy of this repo. Ensure that the following options are checked:
- Discard local changes before syncing
- Delete the project before syncing
- Update revision on job launch

![Ansible ACS project](/docs-images/ansible-acs-project.png)

#### Credentials
Create a new `Vault` credential

![Ansible ACS Vault credentials](/docs-images/ansible-acs-vault.png)

#### Inventory
Create an inventory for `Localhost`, with a host for `127.0.0.1`. Ensure that the `ansible_connection: local` variable is specified for the host.

![Ansible localhost inventory 1](/docs-images/ansible-localhost-inventory-1.png)

![Ansible localhost inventory 2](/docs-images/ansible-localhost-inventory-2.png)

#### Template
Create a new template in Ansible, specifying the following:
- Inventory: Localhost (from above)
- Project: ACS Policy as Code source (from above)
- Playbook: playbooks/update.yml
- Credentials: Vault (from above)

Select 'Enable Webhook' and save the template. A new webhook token will be provided once the template is saved.

![Ansible ACS template 1](/docs-images/ansible-acs-template-1.png)

![Ansible ACS template 2](/docs-images/ansible-acs-template-2.png)

### Webhooks

Navigate to `Settings` in your forked repo and select `Webhooks`.

![Ansible ACS webhook 1](/docs-images/ansible-acs-webhook-1.png)

Select `Add webhook` and configure a new webhook using the Ansible webhook endpoint.

![Ansible ACS webhook 2](/docs-images/ansible-acs-webhook-2.png)

## Running the demo

Find a policy in ACS, such as `Curl in Image`. Verify that the policy is currently disabled.

![Ansible ACS Policy 1](/docs-images/ansible-acs-policy-1.png)

Find the corresponding JSON file, in this case `Curl in Image.json`. Update the `disabled` attribute to `false`.

![Ansible ACS Policy 2](/docs-images/ansible-acs-policy-2.png)

Commit and push the change to your repo, and verify that the Ansible automation jobs start.

![Ansible ACS Policy 3](/docs-images/ansible-acs-policy-3.png)

![Ansible ACS Policy 4](/docs-images/ansible-acs-policy-4.png)

Once the Ansible automation jobs complete, verify that the policy is now `Enabled`.

![Ansible ACS Policy 5](/docs-images/ansible-acs-policy-5.png)

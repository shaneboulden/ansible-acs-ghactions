# Ansible, ACS and Github Actions Policy-as-Code demo

A demonstration showcasing GitOps policy-as-code approaches with Red Hat Advanced Cluster Security for Kubernetes (ACS), Ansible Automation Platform and GitHub Actions.

## Preparing the demo environment
Fork this repo to your own account.

### ACS
Create a new 'StackRox API integration' admin token in Red Hat Advanced Cluster Security for Kubernetes (ACS) and copy the value.

![StackRox API token 1](/docs-images/stackrox-api-token-1.png)

![StackRox API token 2](/docs-images/stackrox-api-token-2.png)

### Ansible
Create the components in Ansible Automation Platform required to orchestrate policy updates.

#### Project
Create a new project, and replace the URL with your forked copy of this repo. Ensure that the following options are checked:
- Discard local changes before syncing
- Delete the project before syncing
- Update revision on job launch

![Ansible ACS project](/docs-images/ansible-acs-project.png)

#### Template
Create a new template in Ansible, specifying the following:
- Inventory: Localhost (from above)
- Project: ACS Policy as Code source (from above)
- Playbook: update.yml

Specify the ACS host and API token as template variables:
```
acs_host: central-stackrox.apps.example.com
acs_token: eyJhb...
```

Select 'Enable Webhook' and save the template. A new webhook token will be provided once the template is saved.

![Ansible ACS template 1](/docs-images/ansible-acs-template-1.png)

![Ansible ACS template 2](/docs-images/ansible-acs-template-2.png)

### GitHub Actions 

Navigate to `Settings` in your forked repo and select `Secrets and variables` -> `Actions`. Add the webhook secret and URL from Ansible as repository secrets named `WEBHOOK_SECRET` and `WEBHOOK_URL`.

![GitHub Actions secrets](/docs-images/actions-secrets.png)

## Running the demo

Find a policy in ACS, such as `Curl in Image`. Verify that the policy is currently disabled.

![Ansible ACS Policy 1](/docs-images/ansible-acs-policy-1.png)

Find the corresponding JSON file, in this case `Curl in Image.json`. Update the `disabled` attribute to `false`.

![Ansible ACS Policy 2](/docs-images/ansible-acs-policy-2.png)

Commit and push the change to your repo, and verify that the GitHub Actions workflow kicks off to run `ansible-lint` and start the playbooks via the Ansible Automation Platform RESTful API.

![GitHub actions output](/docs-images/actions-output.png)

Verify that the Ansible automation jobs start.

![Ansible ACS Policy 3](/docs-images/ansible-acs-policy-3.png)

![Ansible ACS Policy 4](/docs-images/ansible-acs-policy-4.png)

Once the Ansible automation jobs complete, verify that the policy is now `Enabled`.

![Ansible ACS Policy 5](/docs-images/ansible-acs-policy-5.png)

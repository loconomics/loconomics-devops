# Introduction

This repository contains a [Terraform](https://terraform.io) script to provision infrastructure for the new Loconomics backend. It brings the following benefits:

 * Idempotent operations. Running the script again won't reprovision something that already exists and hasn't changed.
 * Change management. Need a new service, or need to change an existing one? Simply change the script and run `terraform up` with confidence.
 * Create and destroy separate environments quickly. Test changes by creating an environment identical to staging, then destroy everything associated with that environment when it is no longer needed.
 * Automate everything. Potential partners can create their own backend deployments with ease, and can keep them updated with any infrastructure changes you make.
 * Collaborate on infrastructure. If a partner identifies a possible improvement to this setup, simply merge a pull request and reprovision everything. Only new changes are applied where possible.
 * Track shared state easily. Infrastructure provisioned by one team member can be examined by another, and changes that are made propagate across the organization.

# How to Use

1. [Download Terraform](https://www.terraform.io/downloads.html).
2. Configure [remote state](https://www.terraform.io/docs/state/remote.html) for Azure. This ensures that changes you make are visible to others.
3. [Pick your workspace](https://www.terraform.io/docs/state/workspaces.html). Workspaces are like environments. For instance, the "staging" workspace holds all resources associated with the staging environment. If you don't pick a workspace, "default" is used.
4. Run `terraform up` to provision everything, and `terraform destroy` to tear it all down.

This script performs the following actions:

 * Provisions a new resource group to isolate resources for a given environment
 * Provisions a SQLServer instance and database to isolate data for a given environment
 * Selects a strong random admin password for the database server
 * Creates some firewall rules to allow database access. This should probably be revisited to use the actual IP of the originating computer somehow. Firewall access is needed for later steps.
 * Provisions a firewall rule to allow the app to access the database. This should probably also be revisited to use the app server's actual IP.
 * Takes a snapshot of the dev.loconomics.com database and loads it into the newly-provisioned instance. An official database seed process would be a better approach.
 * Provisions a service plan for the app
 * Provisions the necessary Linux-based Docker server to run the app. The container is configured with the provisioned database server and credentials.

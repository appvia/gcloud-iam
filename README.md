# gcloud-iam


## Overview

This repository is a collection of information and examples / templates, for managing google cloud platform

### Example IAM role Creation

If you are looking to create example roles, you can use these templates
[example role](roles/example_org_role.yaml)
[example project role](roles/example_project_role.yaml)

These relate to templates of which you can reference by using [external resources](https://cloud.google.com/deployment-manager/docs/configuration/templates/hosting-templates-externally)

### Project Automation

If you are aiming for full automation of your entire workflow, from project creation through to profiles, roles and permissions of users and service accounts within the organisation and within projects, then there are several steps to follow.

There is customised python code for this process, split into specific categories:
- Project creation code
- Service Account Creation for the project
- API enablement


### Pre-requisites

Before you do anything you will need to create your organization inside Google cloud. For information on a few approaches, [read here](docs/create_org.md)

### Process

1. Create a project that will be responsible for creating other projects. This project will be used as a deployment manager for project creation for managing your organisation.

`gcloud projects create myexample-project --organization <org_id>`


2. Activate specific API's so that it has the abilitiy to manage what it needs to
  * Deployment Manager API
  * Resource Manager API
  * Billing API
  * IAM API
  * Service Management API
  ```
  gcloud services enable deploymentmanager.googleapis.com \
  cloudbilling.googleapis.com cloudresourcemanager.googleapis.com \
  servicemanagement.googleapis.com iam.googleapis.com
  ```
3. Fetch the Cloud Service service account associated with the project created in 1. Named <project_number>@cloudservices.gserviceaccount.com
4. Give the Service Account permissions to the root org node, **read the pre-requisites**
  * Project Creator : `roles/resourcemanager.projectCreator`

  `gcloud organizations add-iam-policy-binding <org_id> --member serviceAccount:xxxxxxxx@cloudservices.gserviceaccount.com  --role roles/resourcemanager.projectCreator`

  * Billing Account User : `roles/billing.user` **We would expect the billing account to be setup as a pre-req**

  `gcloud organizations add-iam-policy-binding <org_id> --member serviceAccount:xxxxxxxx@cloudservices.gserviceaccount.com  --role roles/billing.user`


Once these steps are complete you now have a way of automating project creation. This project above, should never be used for anything but project creation.

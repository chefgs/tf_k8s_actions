## GitHub Action + Dev 2023 Submission

## What I built 
- I've created Terraform code that will create a namespace and deploys the Nginx server in the minikube cluster and TF code verification and deployment has been automated using GitHub actions

## How I built
- Created a GitHub Actions workflow using the Marketplace Github actions plugins,
    - `actions/checkout@v2.5.0` -> to Checkout the code
    - `medyagh/setup-minikube@v0.0.13` -> to setup `minikube`
    - `Azure/setup-kubectl@v3` -> to setup `kubectl`
    - `hashicorp/setup-terraform@v2.0.2` -> to setup `terraform`
- This workflow can be used in development environments, in which an Infra developer can create the Terraform code to deploy kubernetes workload. Once after creating the tf code, the developer can trigger the Terraform workflow, that will do the CI for Terraform code, and deploy the infra in `minikube`.
- The kube config context has been created as a variable in Terraform, so it can be overridden with other Kubernetes Cluster config and contexts from Cloud providers like Amazon EKS or Azure AKS or GCP GKE Clusters.

## GitHub Actions Brief Introduction
- GitHub Actions workflow can be used to automate the CICD for software deployments and running various stages for software development life cycle.
- As per the [documentation](https://docs.github.com/en/actions)

> GitHub Actions is a continuous integration and continuous delivery (CI/CD) platform that allows you to automate your build, test, and deployment pipeline. You can create workflows that build and test every pull request to your repository, or deploy merged pull requests to production.

> GitHub Actions goes beyond just DevOps and lets you run workflows when other events happen in your repository. For example, you can run a workflow to automatically add the appropriate labels whenever someone creates a new issue in your repository.
> GitHub provides Linux, Windows, and macOS virtual machines to run your workflows, or you can host your own self-hosted runners in your own data center or cloud infrastructure.

## Introduction to Workflow Yaml Blocks
- GitHub action workflow consists of various component blocks,
- `on` block - In this block we'll mention what is the triggering event for the workflow. It controls when the action will run. Workflow runs when manually triggered using the UI
- `workflow_displatch` - It is the sub block inside the `on` event triggering block, in which we can specify what are the inputs needed to trigger the workflow. 
- `jobs` block - In this block we'll define the workflow actions like code building, testing and deployment to environments. A workflow run is made up of one or more `jobs` that can run sequentially or in parallel
- `steps` block - This is a sub block inside `jobs`, where each stages of job will be defined

- After adding a new workflow file in the `$REPO_HOME_PATH/.github/workflows` directory, it will be showing up in the repo `actions` tab. (As shown below) 

![TF workflow trigger](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sm7n7m3rfcaxtxp8rlcp.png)

-  When we create a gh workflow with workflow_dispatch, it has to be pushed into `main` branch. If we try to add it on another branch, the option `run workflow` to manually triggering the workflow won't be visible. Refer the community discussion [here](https://github.com/orgs/community/discussions/25219)   

### App Link
Source code for the Repo is [available here](https://github.com/chefgs/tf_k8s_actions)

Github Action [Workflow Yaml](https://github.com/chefgs/tf_k8s_actions/blob/main/.github/workflows/tf_k8s_workflow.yml)

### Screenshots 

![tf k8s workflow](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/d8ifcj9ohlly83o0v16l.png)


## Description 
### Triggering the Workflow
This workflow can be triggered from the `actions` tab, by providing the Terraform code directory as an input (Refer the screenshot above).
So it will run the below steps in the directory provided as input,
1. Workflow installs, `minikube`, `kubectl` and `terraform` CLI executables needed to be used by the rest of workflow
2. It runs `terraform init` command to download the `kubernetes` provider
3. Then runs `terraform validate` command to check the tf code is valid or not
4. After that it runs, `terraform plan` and `terraform apply`  commands and performs the Kubernetes namespace creation and deploys the nginx server. 
5. Workflow also has `terraform destroy` command, that deletes the kubernetes infra created in the workflow

### Workflow Dispatch
- This workflow uses, `workflow_displatch` Github syntax - It is the sub block inside the on event triggering block, in which we can specify what are the inputs needed to trigger the workflow.
- In the on event block `workflow_dispatch` section, we will be adding the inputs directory path, on which we are going to run our Terraform code validation
- It is defaulted to the 'kubernetes' directory present in the repo for the ease of demo purpose.

### Link to Source Code 
Source code for the Repo is [available here](https://github.com/chefgs/tf_k8s_actions)

Github Action [Workflow Log](https://github.com/chefgs/tf_k8s_actions/actions/runs/5048663621/jobs/9057128988)

Github Action [Workflow Yaml](https://github.com/chefgs/tf_k8s_actions/blob/main/.github/workflows/tf_k8s_workflow.yml)

![GH Action workflow run](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/qt9kh6g1yc6b5bfqwwlu.png)

## Background 
As mentioned earlier,
This workflow (or pipeline) can be configured for testing the terraform code pushed by DevOps engineers/SREs/Developers, and can be triggered whenever there is new tf code is pushed into a specific branch for Kubernetes workload management

### Additional Resources/Info
- [Terraform Kubernetes Provider](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs)
- [Workflow Dispatch Inputs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onworkflow_dispatchinputs)
- [Jobs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobs)
- [Terraform Market place Action](https://github.com/marketplace/actions/hashicorp-setup-terraform)


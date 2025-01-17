## CI/CD Workflow with GitHub Actions and ArgoCD

This project demonstrates a complete CI/CD pipeline setup using **GitHub Actions** for Continuous Integration (CI) and **ArgoCD** for Continuous Deployment (CD). The pipeline is triggered by pushing new code to this repository, which runs tests, builds a Docker image, uploads the image to Docker Hub, and syncs the deployment using ArgoCD based on a YAML configuration file.

![DIAGRAM](https://github.com/user-attachments/assets/2e6d6c69-2d67-449f-b9dc-02f53d52e53b)

### Features

- **Continuous Integration (CI)**:
  - Automatically triggered upon code push to the repository.
  - Executes unit tests on the updated code.
  - Builds a new Docker image upon successful testing.
  - Labels the new created Docker image.
  - Pushes the Docker image to Docker Hub.

- **Continuous Deployment (CD)**:
  - Uses ArgoCD to monitor the repository for changes.
  - Syncs the Kubernetes cluster to deploy the updated Docker image based on the YAML manifest files stored in the repository.

## Prerequisites

- Docker Hub account and repository for storing Docker images --> [DockerDocs - Repositories](https://docs.docker.com/docker-hub/repos/create/)
- Kubernetes cluster (In this instance Kind was used) --> [Kind - Quick Guide](https://kind.sigs.k8s.io/)
- ArgoCD installed and configured in the Kubernetes cluster --> [ArgoCD - Overview](https://argo-cd.readthedocs.io/en/stable/)
- GitHub repository --> [GitHub Docs - Repositories](https://docs.github.com/en/repositories/creating-and-managing-repositories/quickstart-for-repositories)
- GitHub Actions configured --> [GitHub Docs - GitHub Actions](https://docs.github.com/en/actions/writing-workflows/quickstart)

## Workflow Overview

### CI with GitHub Actions

The GitHub Actions workflow is triggered every time a new commit is pushed to the main branch. The workflow does the following:

- Runs tests on the new code.
- Builds a Docker image.
- Labels the Docker image.
- Pushes the Docker image to Docker Hub.

## CD with ArgoCD

ArgoCD is set up to monitor this repository for changes to the Kubernetes deployment configuration. When changes are detected in the YAML files, ArgoCD automatically syncs the Kubernetes cluster with the updated configuration.

### CI GitHub Actions Workflow (`ci.yml`)

Located in `.github/workflows/ci.yml`, this workflow includes the following steps:

- **Test**: Run unit tests on the pushed code.
- **Build Docker Image**: Build a new Docker image based on the code in the repository.
- **Labels the Docker Image**: Labels the newly created image.
- **Push to Docker Hub**: Upload the built Docker image to Docker Hub.

### CD with ArgoCD

The ArgoCD setup monitors the repository for changes in the YAML file that describes the Kubernetes deployment. When a new image is pushed to Docker Hub, the following workflow happens:

- ArgoCD detects the change in the deployment configuration (e.g., the image tag).
- It triggers a sync to the Kubernetes cluster, updating the application with the new image.

### ArgoCD Application Configuration

You will need to create a new ArgoCD application that points to this repository and the `k8s/deployment.yml` file.

#### Example of Configuration:

**Step 1: Install ArgoCD on Kubernetes**
- Create the `argocd` namespace, Install ArgoCD:
  ```bash
  kubectl create namespace argocd
  kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

**Step 2: Expose the ArgoCD Server**
- Patch the service for external access and get the ArgoCD server URL
  ```bash
  kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
  kubectl get svc argocd-server -n argocd

**Step 3: Log in to ArgoCD UI**
- Port forward the ArgoCD server:
  ```bash
  kubectl port-forward svc/argocd-server -n argocd 8080:443
- Open a web browser and navigate to https://localhost:8080.
- Log in with the default username admin and retrieve the password:
  ```bash
  kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
 

**Deploy the Application using ArgoCD UI**
- In the Argo CD web UI, create a new application with the following settings:
    - Application Name: flask-app
    - Project: default
    - Sync Policy: Automatic
    - Repository URL: https://github.com/eshedortal/CI_CD_Workflow_GitHub_ArgoCD
    - Revision: main
    - Path: k8s
    - Cluster URL: https://kubernetes.default.svc
    - Namespace: default
    - Click *Create* and then *Sync* to deploy the application.
 
**Port Forward the Flask Application**
- Forward a local port to the service port of your Flask application:
  ```bash
  kubectl port-forward svc/flask-app 5001:5000

**Access the Flask Application**
- Open your web browser and navigate to http://localhost:5001 to view the running Flask application.

## How to Use

- **Set Up Docker Hub Credentials**: Add your Docker Hub credentials as GitHub secrets.
    - `DOCKER_USERNAME`: Your Docker Hub username.
    - `DOCKER_PASSWORD`: Your Docker Hub password.

- **Set Up ArgoCD**: Create an ArgoCD application as described in the ArgoCD section.

- **Push Changes**: Whenever new code is pushed to the repository, the CI pipeline runs, and a new Docker image is built and pushed to Docker Hub. ArgoCD will sync the changes automatically.

## Conclusion

This setup automates the software lifecycle by integrating **GitHub Actions** for Continuous Integration (CI) and **ArgoCD** for Continuous Deployment (CD). In this project, every push to the repository triggers a CI pipeline that runs tests, builds a Docker image, and pushes it to Docker Hub. Once the image is available, ArgoCD automatically syncs the Kubernetes deployment and updates the application with the new image.

To demonstrate this process, a simple **Flask** application is used as the example. This showcases how the pipeline seamlessly handles testing, building, and deploying your application, ensuring that new changes are reflected in the production environment with minimal effort.


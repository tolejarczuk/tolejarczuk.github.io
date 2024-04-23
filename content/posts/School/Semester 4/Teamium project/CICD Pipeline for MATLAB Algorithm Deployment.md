---
created: 2024-04-23 16:41
author: Tomasz Olejarczuk
title: CI/CD Pipeline for MATLAB Algorithm Deployment
date: 23-04-2024
subject: null
keywords: null
subtitle: null
titlepage: true
titlepage-text-color: '663366'
titlepage-rule-color: '663366'
titlepage-background: null
titlepage-rule-height: null
page-background: Bins/Pandoc-extras/backgrounds/background6.pdf
titlepage-logo: Bins/Pandoc-extras/fontys-logo.pdf
logo-width: 50mm
toc: true
toc-own-page: true
header-left: \hspace{1cm}
header-center: \leftmark
header-right: Page \thepage
footer-left: \thetitle
footer-center: null
footer-right: \theauthor
tags: null
updated: 2024-04-23T17:08
---

# CI/CD Pipeline for MATLAB Algorithm Deployment

# 1. Summary

This document outlines the CI/CD (Continuous Integration/Continuous Delivery) pipeline implemented for the MATLAB algorithm project. Utilizing GitHub Actions, the pipeline automates the process of building, versioning and deploying Docker images containing the MATLAB algorithm to the GitHub Container Registry (GHCR). The pipeline is triggered whenever changes are pushed to the 'main' branch, ensuring that the latest version of the algorithm is readily available for deployment, streamlining the development and release process.

# 2. Introduction

**Purpose:** The CI/CD pipeline aims to streamline the development and deployment workflow for the MATLAB algorithm project. Key objectives include:

* **Automation:** Automate the repetitive tasks of building, testing (if applicable), versioning, and publishing Docker images, reducing manual intervention and potential errors.
* **Consistency:** Enforce a consistent pipeline process, ensuring builds and deployments follow predefined steps, promoting reliability.
* **Agility:** Enable frequent updates to the MATLAB algorithm with the confidence that automated processes will build and deploy changes seamlessly.

**Scope:** The pipeline focuses on the following key components:

* **MATLAB Code Repository:** The source code for the MATLAB algorithm, hosted on GitHub.
* **Docker Images:** The containerized format used to package and deploy the MATLAB algorithm.
* **GitHub Container Registry (GHCR):** The repository for storing the built Docker images.

# 3. Workflow Overview

**Trigger:**

* The CI/CD pipeline is initiated whenever new code is pushed to the 'main' branch of the GitHub repository.

**High-Level Steps:**

1. **Semantic Versioning:**
   
   * The pipeline determines the appropriate version increment (major, minor, or patch) based on the nature of the committed changes.
   * A new version tag is generated and applied to the Docker image.
1. **Docker Image Build:**
   
   * **Base Image (if needed):** The pipeline checks if a base image with the required MATLAB runtime dependencies exists. If not, a new base image is built.
   * **Runtime Image (if needed):** The pipeline checks if a runtime image exists; if not, it's created. This image potentially layers on top of the base image.
   * **Algorithm Image:** The MATLAB algorithm code is packaged into a Docker image, leveraging the runtime (and potentially the base) image as its foundation. This final artifact is given the new version tag and a "latest" tag.
1. **Image Publishing:**
   
   * The newly built and tagged Docker image containing the MATLAB algorithm is pushed to the GitHub Container Registry (GHCR).

````mermaid
graph TD
    A[Code Push to 'main'] --> B[Versioning] 
    B --> C[Build Base Image]
    B --> D[Build Runtime Image]
    B --> E[Build Algorithm Image] 
    C --> F[Push Base Image]
    D --> F[Push Runtime Image]
    E --> F[Push Algorithm Image]
````

![](Bins/Images/mermaid_cicd.png)

# 4. Pipeline Breakdown (Detailed)

**Job: push-matlab-algorithm-image**

* **runs-on:** ubuntu-latest
  * The pipeline executes within an Ubuntu-based environment.

**Steps**

1. **Checkout** (`actions/checkout@v4`)
   
   * Retrieves a copy of your project's code from the GitHub repository. This is essential for subsequent build steps.
1. **Semantic Versioning** (`anothrNick/github-tag-action@1.69.0`)
   
   * **Key Tasks:**
     * Examines the nature of changes in the code push to determine the appropriate version increment (e.g., major.minor.patch).
     * Generates a new unique version tag to be applied to the Docker image.
   * **Note:** Explain how `anothrNick/github-tag-action` integrates with your code analysis or the commit message conventions you might use to drive versioning logic.
1. **GitHub Container Registry (GHCR) Login** (`docker/login-action@v3`)
   
   * Authenticates with GHCR using your GitHub credentials. This grants permission to push images in subsequent steps.
1. **Build Runtime Base Image**
   
   * **Conditional Logic:** If the `ghcr.io/teamiumdev/matlabruntimebase:r2024a` image doesn't already exist, it is built.
   * **Dockerfile:** This build is directed by `Dockerfile.deps`.
   * **Purpose:** This image installs MATLAB Runtime dependencies, providing a common foundation for later builds.
1. **Build Runtime Image**
   
   * **Conditional Logic:** Similarly, the `ghcr.io/teamiumdev/matlabruntime:r2024a` image is only built if it doesn't exist.
   * **Dockerfile:** This step uses `Dockerfile.runtime`.
   * **Purpose :** This layer might add additional dependencies or configuration before packaging the algorithm itself.
1. **Build Inventory Image**
   
   * **Dockerfile:** It uses a dedicated Dockerfile (e.g., `Dockerfile`).
   * **Image Building:** Bundles the MATLAB algorithm code, along with necessary runtime components, creating the final deployable image.
   * **Tagging:**
     * **Version Tag:** The new tag generated in the versioning step is applied.
     * **'latest' Tag:** This tag offers a convenient reference for deployment.
1. **Push Inventory Image**
   
   * **Destination:** Pushes the newly built image to GHCR, along with its tags, making it available for deployment.

## 4.1 Actual code:

````yaml
name: Deploy Images to GHCR and Auto Tag
on:
  push:
    branches:
      - main
jobs:
  push-matlab-algorithm-image:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
      with:
        fetch-depth: '0'
    - name: Minor version for each merge
      id: taggerDryRun
      uses: anothrNick/github-tag-action@1.69.0
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        WITH_V: false
        DRY_RUN: true
    - name: echo new tag
      run: |
        echo "The next tag version will be: ${{ steps.taggerDryRun.outputs.new_tag }}"
    - name: echo tag
      run: |
        echo "The current tag is: ${{ steps.taggerDryRun.outputs.tag }}"
    - name: echo part
      run: |
        echo "The version increment was: ${{ steps.taggerDryRun.outputs.part }}"
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@v4
    - name: 'Login to GitHub Container Registry'
      uses: docker/login-action@v3
      with:
        registry: ghcr.io
        username: ${{github.actor}}
        password: ${{secrets.GITHUB_TOKEN}}
    - name: 'Build runtime base image'
      run: |
        if docker manifest inspect ghcr.io/teamiumdev/matlabruntimebase:r2024a
        then
          echo "It exists. Skip the build..."
        else
          echo "Building new image.."
          docker build . --file "Dockerfile.deps" --tag ghcr.io/teamiumdev/matlabruntimebase:r2024a
          docker push ghcr.io/teamiumdev/matlabruntimebase:r2024a
        fi
    - name: 'Build runtime image'
      run: |
        if docker manifest inspect ghcr.io/teamiumdev/matlabruntime:r2024a
        then
          echo "It exists. Skip the build..."
        else
          echo "Building new image.."
          docker build . --file "Dockerfile.runtime" --tag ghcr.io/teamiumdev/matlabruntime:r2024a
          docker push ghcr.io/teamiumdev/matlabruntime:r2024a
        fi
    - name: 'Build Inventory Image'
      run: docker build . --file Dockerfile --tag ghcr.io/teamiumdev/matlab-algorithm:${{ steps.taggerDryRun.outputs.new_tag }} --tag ghcr.io/teamiumdev/matlab-algorithm:latest
    - name: Minor version for each merge
      id: taggerFinal
      uses: anothrNick/github-tag-action@1.69.0
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        WITH_V: false
    - name: 'Push Inventory Image'
      run: docker push --all-tags ghcr.io/teamiumdev/matlab-algorithm
````

# 5. Deployment Considerations (Future)

Currently, the CI/CD pipeline concludes with the production of Docker images stored in GitHub Container Registry (GHCR). To fully leverage the benefits of containerization and streamlined deployment, here are potential deployment pathways to consider as the project scales:

**Kubernetes for Orchestration:**

* **Benefits:** Kubernetes is a powerful container orchestration platform ideal for managing complex deployments. Key advantages include:
  * **Scalability:** Easily deploy multiple instances of your MATLAB image to handle increased load.
  * **Resilience:** Kubernetes offers self-healing, automatic restarts, and rollout/rollback features for high availability.
  * **Resource Management:** Optimize resource usage across your application components.

**Deployment Tools:**

* **ArgoCD:** A GitOps deployment tool designed for Kubernetes. Synchronizes the desired state defined in Git with the running Kubernetes cluster for continuous deployment.
  * **Advantages:** Declarative configuration, automated updates when code changes, enhanced observability.
* **Jenkins:** A versatile automation server that can be used to orchestrate deployments to Kubernetes.
  * **Advantages:** Extensible, integrates well with existing pipelines and tools.

**Next Steps:**

1. **Containerization:** Ensure your CI/CD pipeline consistently produces deployment-ready Docker images for your MATLAB algorithm.
1. **Deployment Manifests:** Craft Kubernetes deployment manifests (YAML files) that define how your MATLAB Algorithm image should be deployed within the cluster (replicas, resource needs, etc.)
1. **Tool Choice:** Evaluate both ArgoCD and Jenkins, considering factors like team familiarity, desired level of automation, and integration with existing infrastructure.

# 6. Additional Considerations

* **Security:** Address security measures for GitHub Actions secrets and the GHCR access token.
* **Error Handling:** Outline any error reporting or notification mechanisms integrated into the pipeline.
* **Future Improvements:** Potential areas for pipeline optimization (e.g., caching for Docker builds, parallelizing tests, additional automation steps).

---

Last Modified: `=dateformat(this.file.mtime, "DDDD, HH:mm")`

#### 0.1.1. Tags:

`=this.file.tags`

````dataview
List FROM #kubernetes
````

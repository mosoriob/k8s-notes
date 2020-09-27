## How To Automate Deployments to DigitalOcean Kubernetes with CircleCI


This is a fork from a excellent article by [Jonathan Cardoso Machado](https://github.com/JCMais). 
The original article can be found [here](https://www.digitalocean.com/community/tutorials/how-to-automate-deployments-to-digitalocean-kubernetes-with-circleci)

### Intro


Having an automated deployment process is a requirement for a scalable and resilient application, and GitOps, or Git-based DevOps, has rapidly become a popular method of organizing CI/CD with a Git repository as a “single source of truth.” Tools like CircleCI integrate with your GitHub repository, allowing you to test and deploy your code automatically every time you make a change to your repository. When this kind of CI/CD is combined with the flexibility of Kubernetes infrastructure, you can build an application that scales easily with changing demand.

In this article you will use CircleCI to deploy a sample application to a DigitalOcean Kubernetes (DOKS) cluster. After reading this tutorial, you’ll be able to apply these same techniques to deploy other CI/CD tools that are buildable as Docker images.


### Prerequisites

To follow this tutorial, you’ll need to have:

A DigitalOcean account, which you can set up by following the Sign up for a DigitalOcean Account documentation.

Docker installed on your workstation, and knowledge of how to build, remove, and run Docker images. You can install Docker on Ubuntu 18.04 by following the tutorial on [How To Install and Use Docker on Ubuntu 18.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04).

Knowledge of how Kubernetes works and how to create deployments and services on it. It’s highly recommended to read the [Introduction to Kubernetes article](https://www.digitalocean.com/community/tutorials/an-introduction-to-kubernetes).


The [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) command line interface tool installed on the computer from which you will control your cluster.

An account on [Docker Hub](https://hub.docker.com/) to be used to store your sample application image.

A [GitHub account](https://github.com/) and knowledge of Git basics. You can follow the tutorial series [Introduction to Git: Installation, Usage, and Branches](https://www.digitalocean.com/community/tutorial_series/introduction-to-git-installation-usage-and-branches) and [How To Create a Pull Request on GitHub](https://www.digitalocean.com/community/tutorials/how-to-create-a-pull-request-on-github) to build this knowledge.

For this tutorial, you will use Kubernetes version `v1.18.8` and kubectl version `v1.16.6-beta.0` 


### Table of contents

- [Step 1 — Creating Your DigitalOcean Kubernetes Cluster](step1.md)
- [Step 2 — Creating the Local Git Repository](step2.md)
- [Step 3 — Creating a Service Account](step3.md)
- [Step 4 — Creating the Role and the Role Binding](step4.md)
- [Step 5 — Creating Your Sample Application](step5.md)
- [Step 6 — Creating the Kubernetes Deployment and Service](step6.md)
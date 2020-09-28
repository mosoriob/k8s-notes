## Step 6 — Creating the Kubernetes Deployment and Service

With your Docker image created and working, you will now create a manifest telling Kubernetes how to create a Deployment from it on your cluster.


Create the YAML deployment file `do-sample-app/kube/do-sample-deployment.yml` and open it with your text editor

```bash
nano ~/do-sample-app/kube/do-sample-deployment.yml
```

Write the following content on the file, making sure to replace dockerhub-username with your Docker Hub username:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: do-kubernetes-sample-app
  namespace: default
  labels:
    app: do-kubernetes-sample-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: do-kubernetes-sample-app
  template:
    metadata:
      labels:
        app: do-kubernetes-sample-app
    spec:
      containers:
        - name: do-kubernetes-sample-app
          image: $DOCKERUSERNAME/do-kubernetes-sample-app:latest
          ports:
            - containerPort: 80
              name: http
```

Kubernetes deployments are from the API group `apps`, so the `apiVersion` of your manifest is set to apps/v1. On `metadata` you added a new field you have not used previously, called `metadata.labels.` This is useful to organize your deployments. The field `spec` represents the behavior specification of your deployment. A deployment is responsible for managing one or more pods; in this case it’s going to have a single replica by the `spec.replicas` field. That is, it’s going to create and manage a single pod.

To manage pods, your deployment must know which pods it’s responsible for. The `spec.selector` field is the one that gives it that information. In this case the deployment will be responsible for all pods with tags `app=do-kubernetes-sample-app`. The `spec.template` field contains the details of the `Pod` this deployment will create. Inside the template you also have a `spec.template.metadata` field. The `labels` inside this field must match the ones used on `spec.selector`. `spec.template.spec` is the specification of the pod itself. In this case it contains a single container, called `do-kubernetes-sample-app`. The image of that container is the image you built previously and pushed to Docker Hub.

This YAML file also tells Kubernetes that this container exposes the port `80`, and gives this port the name `http`.

To access the port exposed by your `Deployment`, create a Service. Make a file named `do-sample-app/kube/do-sample-service.yml` and open it with your favorite editor


```bash
vim do-sample-app/kube/do-sample-service.yml
```

Next, add the following lines to the file:

```yml
apiVersion: v1
kind: Service
metadata:
  name: do-kubernetes-sample-app
  namespace: default
  labels:
    app: do-kubernetes-sample-app
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: http
      name: http
  selector:
    app: do-kubernetes-sample-app


```

This file gives your `Service` the same labels used on your deployment. This is not required, but it helps to organize your applications on Kubernetes.

The service resource also has a `spec` field. The `spec.type` field is responsible for the behavior of the service. In this case it’s a `ClusterIP`, which means the service is exposed on a cluster-internal IP, and is only reachable from within your cluster. This is the default `spec.type` for services. `spec.selector` is the label selector criteria that should be used when picking the pods to be exposed by this service. Since your pod has the tag `app: do-kubernetes-sample-app`, you used it here. `spec.ports` are the ports exposed by the pod’s containers that you want to expose from this service. Your pod has a single container which exposes port `80`, named `http`, so you are using it here as targetPort. The service exposes that port on port `80` too, with the same name, but you could have used a different port/name combination than the one from the container.

With your `Service` and `Deployment` manifest files created, you can now create those resources on your Kubernetes cluster using `kubectl`:

```
kubectl apply -f do-sample-app/kube/
```

You will receive the following output:

```bash
Output
deployment.apps/do-kubernetes-sample-app created
service/do-kubernetes-sample-app created
```

Test if this is working by forwarding one port on your machine to the port that the service is exposing inside your Kubernetes cluster. You can do that using kubectl `port-forward`:

```
kubectl port-forward $(kubectl get pod --selector="app=do-kubernetes-sample-app" --output jsonpath='{.items[0].metadata.name}') 8080:80
```

The subshell command `$(kubectl get pod --selector="app=do-kubernetes-sample-app" --output jsonpath='{.items[0].metadata.name}')` retrieves the name of the pod matching the tag you used. Otherwise you could have retrieved it from the list of pods by using kubectl get pods.

After you run `port-forward`, the shell will stop being interactive, and will instead output the requests redirected to your cluster:

```
Output
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80

```

Opening localhost:8080 on any browser should render the same page you saw when you ran the container locally, but it’s now coming from your Kubernetes cluster! As before, you can also use curl in a new terminal window to check if it’s working:

```
Output
<!DOCTYPE html>
<title>DigitalOcean</title>
<body>
  Kubernetes Sample Application
</body>
```

Next, it’s time to push all the files you created to your GitHub repository. To do this you must first create a repository on GitHub called digital-ocean-kubernetes-deploy.

In order to keep this repository simple for demonstration purposes, do not initialize the new repository with a README, license, or .gitignore file when asked on the GitHub UI. You can add these files later on.

With the repository created, point your local repository to the one on GitHub. To do this, press CTRL + C to stop kubectl port-forward and get the command line back, then run the following commands to add a new remote called origin:

```
cd ~/do-sample-app/
git remote add origin https://github.com/your-github-account-username/digital-ocean-kubernetes-deploy.git
```



There should be no output from the preceding command.

Next, commit all the files you created up to now to the GitHub repository. First, add the files:

```
git add --all
```

Next, commit the files to your repository, with a commit message in quotation marks:

```
git commit -m "initial commit"
```

This will yield output similar to the following:

```
[master (root-commit) db321ad] initial commit
 4 files changed, 47 insertions(+)
 create mode 100644 Dockerfile
 create mode 100644 index.html
 create mode 100644 kube/do-sample-deployment.yml
 create mode 100644 kube/do-sample-service.yml
```

Finally, push the files to GitHub:

```
git push -u origin master
```

You will be prompted for your username and password. Once you have entered this, you will see output like this:

```
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 907 bytes | 0 bytes/s, done.
Total 7 (delta 0), reused 0 (delta 0)
To github.com:your-github-account-username/digital-ocean-kubernetes-deploy.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
```

If you go to your GitHub repository page you will now see all the files there. With your project up on GitHub, you can now set up CircleCI as your CI/CD tool.



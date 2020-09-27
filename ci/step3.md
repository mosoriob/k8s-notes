### Step 3 — Creating a Service Account

It’s generally not recommended to use the default admin user to authenticate from other Services into your Kubernetes cluster. If your keys on the external provider got compromised, your whole cluster would become compromised.

Instead you are going to use a single [Service Account](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/) with a specific Role, which is all part of the [RBAC Kubernetes authorization model](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

This authorization model is based on Roles and Resources. You start by creating a Service Account, which is basically a user on your cluster, then you create a Role, in which you specify what resources it has access to on your cluster. Finally, you create a Role Binding, which is used to make the connection between the Role and the Service Account previously created, granting to the Service Account access to all resources the Role has access to.

The first Kubernetes resource you are going to create is the Service Account for your CI/CD user, which this tutorial will name cicd.

Create the file `cicd-service-account.yml` inside the `kube-general` directory, and open it with your favorite text editor:

```yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: cicd
  namespace: default
```



This is a YAML file; all Kubernetes resources are represented using one. In this case you are saying this resource is from Kubernetes API version v1 (internally kubectl creates resources by calling Kubernetes HTTP APIs), and it is a `ServiceAccount`.

The `metadata` field is used to add more information about this resource. In this case, you are giving this `ServiceAccount` the name `cicd`, and creating it on the `default` namespace.

```bash
$ kubectl apply -f kube-general/
```

To make sure your `Service Account` is working, try to log in to your cluster using it. To do that you first need to obtain their respective access token and store it in an environment variable. Every Service Account has an access token which Kubernetes stores as a Secret.

You can retrieve this secret using the following command:

```bash
TOKEN=$(kubectl get secret $(kubectl get secret | grep cicd-token | awk '{print $1}') -o jsonpath='{.data.token}' | base64 --decode)
```

You can now try to retrieve your pods with the cicd Service Account. 
Copy the server url from your `~kube/config` and creates a new variable called `SERVER`

```
SERVER="https://e6f66e3d-ce15-4721-91fe-39d28358c5e0.k8s.ondigitalocean.com"
```

Run the following command. This command will give a specific error that you will learn about later in this tutorial:


```bash
$ kubectl --insecure-skip-tls-verify --kubeconfig="/dev/null" --server=$SERVER --token=$TOKEN get pods
```

--insecure-skip-tls-verify skips the step of verifying the certificate of the server, since you are just testing and do not need to verify this. --kubeconfig="/dev/null" is to make sure kubectl does not read your config file and credentials but instead uses the token provided.

The output should be similar to this:

```bash
Error from server (Forbidden): pods is forbidden: User "system:serviceaccount:default:cicd" cannot list resource "pods" in API group "" in the namespace "default"
```

This is an error, but it shows us that the token worked. The error you received is about your Service Account not having the neccessary authorization to list the resource secrets, but you were able to access the server itself. If your token had not worked, the error would have been the following one:

```bash
error: You must be logged in to the server (Unauthorized)
```

Now that the authentication was a success, the next step is to fix the authorization error for the Service Account. You will do this by creating a role with the necessary permissions and binding it to your Service Account.

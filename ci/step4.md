Step 4 — Creating the Role and the Role Binding

Kubernetes has two ways to define roles: using a Role or a ClusterRole resource. The difference between the former and the latter is that the first one applies to a single namespace, while the other is valid for the whole cluster.

As you are using a single namespace on this tutorial, you will use a Role.

kube-general/cicd-role.yml

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: cicd
  namespace: default
rules:
  - apiGroups: ["", "apps", "batch", "extensions"]
    resources: ["deployments", "services", "replicasets", "pods", "jobs", "cronjobs"]
    verbs: ["*"]
```

This YAML has some similarities with the one you created previously, but here you are saying this resource is a Role, and it’s from the Kubernetes API rbac.authorization.k8s.io/v1. You are naming your role cicd, and creating it on the same namespace you created your ServiceAccount, the default one.


Then you have the rules field, which is a list of resources this role has access to. In Kubernetes resources are defined based on the API group they belong to, the resource kind itself, and what actions you can do on then, which is represented by a verb. Those verbs are similar to the HTTP ones.

In our case you are saying that your Role is allowed to do everything, *, on the following resources: deployments, services, replicasets, pods, jobs, and cronjobs. This also applies to those resources belonging to the following API groups: "" (empty string), apps, batch, and extensions. The empty string means the root API group. If you use apiVersion: v1 when creating a resource it means this resource is part of this API group.

A Role by itself does nothing; you must also create a RoleBinding, which binds a Role to something, in this case, a ServiceAccount.

Create the file kube-general/cicd-role-binding.yml and open it:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: cicd
  namespace: default
subjects:
  - kind: ServiceAccount
    name: cicd
    namespace: default
roleRef:
  kind: Role
  name: cicd
  apiGroup: rbac.authorization.k8s.io
```


Your RoleBinding has some specific fields that have not yet been covered in this tutorial. roleRef is the Role you want to bind to something; in this case it is the cicd role you created earlier. subjects is the list of resources you are binding your role to; in this case it’s a single ServiceAccount called cicd.

```note
Note: If you had used a ClusterRole, you would have to create a ClusterRoleBinding instead of a RoleBinding. The file would be almost the same. The only difference would be that it would have no namespace field inside the metadata. 
```

With those files created you will be able to use kubectl apply again. Create those new resources on your Kubernetes cluster by running the following command:

```bash
kubectl apply -f kube-general/
```

You will receive output similar to the following:

```bash
rolebinding.rbac.authorization.k8s.io/cicd created
role.rbac.authorization.k8s.io/cicd created
serviceaccount/cicd created
```

Now, try the command you ran previously:

```bash
kubectl --insecure-skip-tls-verify --kubeconfig="/dev/null" --server=$SERVER --token=$TOKEN get pods
```

Since you have no pods, this will yield the following output:

```
No resources found.
```

In this step, you gave the Service Account you are going to use on CircleCI the necessary authorization to do meaningful actions on your cluster like listing, creating, and updating resources. Now it’s time to create your sample application.

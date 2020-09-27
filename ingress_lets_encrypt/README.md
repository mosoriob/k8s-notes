Based by https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes


## Create dummy services

Then, create the Kubernetes resources using kubectl apply with the -f flag, specifying the file you just saved as a parameter:

```bash
$ kubectl apply -f echo1.yaml
Output
service/echo1 created
deployment.apps/echo1 create
```

Verify that the Service started correctly by confirming that it has a ClusterIP, the internal IP on which the Service is exposed:

```bash
$ kubectl get svc echo1
Output
NAME      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
echo1     ClusterIP   10.245.222.129   <none>        80/TCP    60s
```

```
$ kubectl apply -f echo2.yaml
$ kubectl get svc echo2
```

## Step 2 — Setting Up the Kubernetes Nginx Ingress Controller

```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.34.1/deploy/static/provider/do/deploy.yaml
```


Confirm that the Ingress Controller Pods have started:

```bash
$ kubectl get pods -n ingress-nginx \
      -l app.kubernetes.io/name=ingress-nginx --watch
```

Now, confirm that the DigitalOcean Load Balancer was successfully created by fetching the Service details with kubectl:

```bash

$ kubectl get svc --namespace=ingress-nginx
```

Detect the version of ingress-nginx

```
POD_NAMESPACE=ingress-nginx
POD_NAME=$(kubectl get pods -n $POD_NAMESPACE -l app.kubernetes.io/name=ingress-nginx --field-selector=status.phase=Running -o jsonpath='{.items[0].metadata.name}')
kubectl exec -it $POD_NAME -n $POD_NAMESPACE -- /nginx-ingress-controller --version
```

## Step 3 — Creating the Ingress Resource

```
$ kubectl apply -f echo_ingress.yaml
```

## Step 4 - Install cert

```
$ kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.0.1/cert-manager.yaml
```

To verify our installation, check the cert-manager Namespace for running pods:


```bash
$ kubectl get pods --namespace cert-manager
```


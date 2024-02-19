# Description
PoC Minio Object Storage using Minio Operator from minikube

## Start minikube

Start minikube cluster
```sh
$ minikube start
```

Activate Dashboard

```sh
$ minikube dashboard
```

## Install minio from help

Install helm official repository to access to Minio Operator Web Console

```sh
$ helm repo add minio-operator https://operator.min.io
```

Install helm Minio Operator release in minikube, wait and check all kubernetes resources created from release

```sh
$ helm install \
  --namespace minio-operator \
  --create-namespace \
  operator minio-operator/operator

helm install \
>   --namespace minio-operator \
>   --create-namespace \
>   operator minio-operator/operator
NAME: operator
LAST DEPLOYED: Mon Feb 19 13:59:50 2024
NAMESPACE: minio-operator
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
1. Get the JWT for logging in to the console:
kubectl apply -f - <<EOF
apiVersion: v1
kind: Secret
metadata:
  name: console-sa-secret
  namespace: minio-operator
  annotations:
    kubernetes.io/service-account.name: console-sa
type: kubernetes.io/service-account-token
EOF
kubectl -n minio-operator get secret console-sa-secret -o jsonpath="{.data.token}" | base64 --decode

2. Get the Operator Console URL by running these commands:
  kubectl --namespace minio-operator port-forward svc/console 9090:9090
  echo "Visit the Operator Console at http://127.0.0.1:9090

$ kubectl get all -n minio-operator
```

We can get the Minio Management console Token from **console-sa-secret** secret located in **minio-operator** namespace

![Tenant Token](./images/tenant_token.png "Tenant Token")


## Access to Minio Console Web UI

Create a port forward to connect to Minio Operator Web Console, to create tenants

```sh
$ kubectl port-forward svc/console -n minio-operator 9090:9090
```

Connect to Minio Console Web Console and create a tenant. Use this uri: 

```sh
http://localhost:9090
```

## Create a namesapce for the tenant 

Create a namespace for the tenant

```sh
kubectl create namespace gsdpi
```

## Create the tenant 

Tenant called **gsdpi** with the minimal configuration from Minio Console Web Console:

- **Number of Servers**: 1
- **Drives per Server (Volumes)**: 4
- **Total Size (Sixe per drive)**: 10Gb

![Tenant Uniovi](./images/tenant_config.png "Tenant Uniovi")

Tenant State:

![Tenant State](./images/tenant_state.png "Tenant State")

Tenant State Resume:

![Tenant State Resume"](./images/tenant_resume.png "Tenant State Resume")

Tenant Metrics:

![Tenant Metrics](./images/tenan_metrics.png "Tenant Metrics")

```sh
$ kubectl port-forward svc/gsdpi-hl 9000:9000
```

## Install minio console in Mac

Install the minio CLI to manage your tenants from shell

```sh
$ brew install minio/stable/mc
```

## Install minio console in Linux
```sh
curl https://dl.min.io/client/mc/release/linux-amd64/mc --create-dirs -o $HOME/minio-binaries/mc

chmod +x $HOME/minio-binaries/mc

mv $HOME/minio-binaries/mc /user/local/bin
```

## Somes tests with minio console CLI

Using the **access_token** and **secret_key** from tenant create the alias in unsecure mode, becaue the TLS created by minio is autosigned. These credentials are not save in secrets, so we must to save the credentials.json download after create the tenant:

```sh
mc alias set minikube https://localhost:9000 BsvW9jlpYX8TvD9F HrGdJapKsXbKEcXABWNQ2CO15v3y9MMk --insecure
Added `minikube` successfully.
```

Check the state of the tenant gsdpi
```sh
mc admin info minikube --insecure
●  localhost:9000
   Uptime: 4 minutes 
   Version: 2024-02-17T01:15:57Z
   Network: 1/1 OK 
   Drives: 4/4 OK 
   Pool: 1

Pools:
   1st, Erasure sets: 1, Drives per erasure set: 4

4 drives online, 0 drives offline
```
## List files in a bucket

```sh
$ mc ls minikube --recursive --insecure
```

## Open Tenant Management Console

We can **Open Tenant Management Console** of the gdpi to manage buckets and minio resources of this tenant. Click in the **Minio Management Console button** of the Tenant gsdpi showed in the capture bellow

![Tenant Management console](./images/tenant_management_console.png "Tenant Management console")

List Tenant Buckets

![Tenant Buckets](./images/tenant_buckets.png "Tenant Buckets")

# Description
PoC Minio Object Storage in minikube

# Install minio from help

Install helm official repository

```sh
$ helm repo add minio-operator https://operator.min.io
```

Install helm release in minikube, wait and recover all kubernetes resources created

```sh
$ helm install \
  --namespace minio-operator \
  --create-namespace \
  operator minio-operator/operator

$ kubectl get all -n minio-operator
```

# Access to minio console

Create a port forward to connect to minio web console manager

```sh
$ kubectl port-forward svc/console -n minio-operator 9090:9090
```

Connect to minio console web manager and create a tenant

```sh
http://localhost:9090
```

# Install minio console 

Install the minio CLI to manage your tennat from shell

```sh
$ brew install minio/stable/mc
```

Using the **access_token** and **secret_key** from tenant create the alias in unsecure mode, becaue the TLS created by minio is autosigned

```sh
mc alias set minikube-minio https://localhost:4223 G2KWX8DseVnRtsOe 9MFeIvvxIvsl9xV0YbG08TUe5X9Qi1bs --insecure
Added `minikube-minio` successfully.
```
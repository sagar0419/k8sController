# k8sController

## To create a TLS certs 🔒 use follwing command

```

openssl genrsa -out ca.key 2048

openssl req -new -x509 -days 365 -key ca.key \
  -subj "/C=AU/CN=simple-kubernetes-webhook"\
  -out ca.crt

openssl req -newkey rsa:2048 -nodes -keyout server.key \
  -subj "/C=AU/CN=simple-kubernetes-webhook" \
  -out server.csr

openssl x509 -req \
  -extfile <(printf "subjectAltName=DNS:image-validation-controller.default.svc,DNS:image-validation-controller.default.svc.cluster.local") \
  -days 365 \
  -in server.csr \
  -CA ca.crt -CAkey ca.key -CAcreateserial \
  -out server.crt

  kubectl create secret tls simple-kubernetes-webhook-tls \
  --cert=tls.crt \
  --key=tls.key \
  --dry-run=client -o yaml \
  > ./secret.yaml


cat ca.crt | base64 | tr -d '\n'

rm ca.crt ca.key ca.srl server.crt server.csr server.key
```

## Usage ⚙️

Run these commands in the Makefile directory 🗂️:

`make deploy          # Apply ServiceAccount + Deployment`

`make status          # See what’s running`

`make clean           # Delete all deployed resources`

You can override the namespace if needed:

`make deploy NAMESPACE=kube-system`
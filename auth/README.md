# Setup procedure

https://dexidp.io/docs/guides/kubernetes/
https://github.com/dexidp/dex/blob/master/examples/config-ad-kubelogin.yaml


## Step 1. Generate TLS assets for dex

```bash
cd dex-k8s
./gencer.tsh
```


Copy the content of `ssl/ca.pem` to `files/config.yaml`


## Step 2. Create a Kind cluster

```bash
kind create cluster --config kind.yaml
```

This will load the auth configuration to k8s.


## Step 3. Install Dex

```bash
cd dex-k8s/
kubectl create namespace dex
kubectl -n dex create secret tls dex.tls --cert=ssl/cert.pem --key=ssl/key.pem
kubectl apply -f dex.yaml
```

## Step 4. Test login

```bash
cd dex/examples/example-app
go build
./example-app --issuer https://localhost:32000 --issuer-root-ca ../../../dex-k8s/ssl/ca.pem
```

## Step 5. Create a role and rolebinding

```bash
kubectl create clusterrolebinding oidc-cluster-admin --clusterrole=cluster-admin --user=admin@example.com
```


## Step 6. Install kubelogin and generate kubeconfig


See https://github.com/int128/kubelogin

```bash
brew install kubelogin

kubectl config set-credentials oidc \
  --exec-interactive-mode=Never \
  --exec-api-version=client.authentication.k8s.io/v1 \
  --exec-command=kubectl \
  --exec-arg=oidc-login \
  --exec-arg=get-token \
  --exec-arg=--oidc-extra-scope=email \
  --exec-arg=--oidc-issuer-url=https://localhost:32000 \
  --exec-arg=--oidc-client-id=kubernetes \
  --exec-arg=--oidc-client-secret=ZXhhbXBsZS1hcHAtc2VjcmV0 \
  --exec-arg=--certificate-authority=/Users/kenji/dev/go/src/github.com/kkaneda/random/auth/dex-k8s/ssl/ca.pem
```

# Create a test cluster
kind create cluster --config kind.yaml


# Install Dex


https://dexidp.io/docs/guides/kubernetes/
https://github.com/dexidp/dex/blob/master/examples/config-ad-kubelogin.yaml

Generate TLS assets for dex.
Spin up a Kubernetes cluster with the appropriate flags and CA volume mount.
Create secrets for TLS and for your GitHub OAuth2 client credentials.
Deploy dex.

cd dex/
cd examples/k8s
./gencert.sh

# rename

Note that the ca.pem from above has been renamed to openid-ca.pem in this example - this is just to separate it from any other CA certificates that may be in use.


$ kubectl -n dex create secret tls dex.tls --cert=ssl/cert.pem --key=ssl/key.pem

$ kubectl -n dex create secret \
    generic github-client \
    --from-literal=client-id=$GITHUB_CLIENT_ID \
    --from-literal=client-secret=$GITHUB_CLIENT_SECRET

$ kubectl create -f dex.yaml



# Use a node-port



# Test login

cd examples/example-app
go install .

$ example-app --issuer https://dex.example.com:32000 --issuer-root-ca examples/k8s/ssl/ca.pem

# Test kubelogin

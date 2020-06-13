### Configure RBAC in your Kubernetes Cluster
=========================================

Steps:

1. Create a namespace: `k create -f ns.yaml`
2. Create the user credentials
  * Create a private key for your user - give the file a name
   `openssl genrsa -out robel.key 2048`
  * Create a certificate sign request employee.csr using the private key you just created (robel.key in this example). Make sure you specify your username and group in the -subj section
   `openssl req -new -key robel.key -out robel.csr -subj "/CN=robel/O=robelgroup"`
  * Locate your k8s cluster certificate authority (CA). This will be responsible
for approving the request and generating the necessary certs to access the
cluster API. It's location is normally `/etc/kubernetes/pki` ( in k3s
`/var/lib/rancher/k3s/server/tls/` - the `client-ca.key` and `client-ca.crt`) 
 * Generate the final certificate `robel.crt` by approving the certificate sing request, `robel.csr`, you made earlier.
  In this example the cert will be valid for 500 days.
  `openssl x509 -req -in robel.csr -CA client-ca.crt -CAkey client-ca.key -CAcreateserial -out robel.crt -days 500`
 * Save both `.crt` and `.key` in a safe location
 * Add a new context with the new credentials for your k8s cluster.  
  ```
   k config set-credentials robel --client-certificate=/Users/snowball/Projects/git/pi-k3s/rbac/certs/.  robel.crt --client-key=/Users/snowball/Projects/git/pi-k3s/rbac/certs/robel.key
k config set-context robel-context --cluster=pi-k3s --namespace=aurelius --user=robel
 ```
3. Now you should get access denied error when using kubectl CLI with this
configuration file, as we have not defined yet any permitted operations for
this user. Try `kubectl --context=robel-context get pods`
Create the role for managing deployments

```
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  namespace: aurelius
  name: deployment-manager
rules:
  - apiGroups: ["", "extensions", "apps"]
    resources: ["deployments", "replicaset", "pods"]
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"] # You can also use ["*"]  
```
Bind the role to the robel user
```
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  namespace: aurelius
  name: deployment-manager-binding
subjects:
  - kind: User
    name: robel
    apiGroup: ""
roleRef:
   kind: Role
   name: deployment-manager
   apiGroup: ""
```
Test the RBAC rule

```bash
kubectl --context=robel-context run --image bitnami/dokuwiki mydokuwiki
kubectl --context=robel-context get pods
```

If you run the same command with the --namespace=default argument, it will fail, as the employee user does not have access to this namespace.

```bash
kubectl --context=robel-context get pods --namespace=default
```



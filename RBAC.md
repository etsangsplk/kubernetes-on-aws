# RBAC

## Certs based Auth

This method uses certificates to identify uers.

### Create User

Generate Certs for Superadmin:

```
$ sudo su -
$ cd /etc/kubernetes/ssl
$ openssl genrsa -out sloka.key 2048
$ openssl req -new -key sloka.key -out sloka.csr -subj "/CN=sloka/O=cluster-admin"
$ openssl x509 -req -in sloka.csr -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out sloka.pem -days 10000
```

Setup Kubeconfig:

Base64 encode the certs and copy to `client-certificate-data` & `client-key-data` in `~/.kube/config` configuration file under `user` section:

```
$ base64 -w0 sloka.pem && echo
$ base64 -w0 sloka.key && echo
```

### Bind the user to a Role

This binds our new user to the `cluster-admin` role which allows them to do anything:

```
 kind: ClusterRoleBinding
  apiVersion: rbac.authorization.k8s.io/v1beta1
  metadata:
    name: deployment-manager-binding
    namespace: office
  subjects:
  - kind: User
    name: sloka
    apiGroup: ""
  roleRef:
    kind: Role
    name: cluster-admin
    apiGroup: ""
```
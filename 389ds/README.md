# 389ds

Two kustomization bases that allow deploying the 389ds LDAP server

- __base__: Deploys an in-cluster LDAP server
- __nodeport__: Deploys an in-cluster LDAP server exposed at 30389 (LDAP) and 30636 (LDAPS) via `NodePort`
- __nodeport-secure__: Deploys an in-cluster LDAP server exposed at 30636 (LDAPS) via `NodePort`

## Quickstart

- Create a secure password for the LDAP server
  - Autogenerate with: `sed -i -e "s/myDMPassword/$(tr -dc 'a-zA-Z0-9' < /dev/urandom | head -c 16)/" base/389ds.env`
  - Manually edit the `base/389ds.env`
- Deploy with `kubectl apply -k <choosen_base>`
- Initialize the backend
  - `export POD_NAME="$(kubectl get pod -l app.kubernetes.io/name=389ds -o jsonpath='{.items[0].metadata.name}')"`
  - `kubectl exec "$POD_NAME" -- dsconf localhost backend create --suffix 'dc=example,dc=com' --be-name userroot`

## Accessing the LDAP server

The default username for the directory manager is `cn='Directory Manager'`

Grab the password for the directory manager: `export DM_PASS=$(kubectl get secret $(kubectl get secrets -l app.kubernetes.io/name=389ds -o jsonpath='{.items[0].metadata.name}') -o jsonpath='{.data.DS_DM_PASSWORD}' | base64 -d)`

```sh
LDAPTLS_REQCERT=never ldapsearch -H ldaps://<address>:<port> -D 'cn=Directory Manager' -w "$DM_PASS"
```

### Via port forwarding

Forward LDAP port to localhost:3389 using `kubectl port-forward $(kubectl get pod -l app.kubernetes.io/name=389ds -o jsonpath='{.items[0].metadata.name}') 3389:389`

### Via nodeport

Access the LDAP server at `<nodeIP>:30389` (LDAP protocol) or `<nodeIP>:30636` (LDAPs protocol)

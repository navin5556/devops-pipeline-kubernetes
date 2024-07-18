* Create Servcie account/ROLE/BIND-ROLE/Token

## Create Service Account, Role & Assign that role, And create a secret for Service Account and geenrate a Token

### Creating Service Account which will be used by jenkins to deploy the app in webapps namespace using jenkins user

First create namespace
```
kubectl create ns webapps
```

vim svm.yaml
kubectl apply -f svm.yaml
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: webapps
```

### Create Role 

Create a file named `role.yaml` using the command `vim role.yaml`. Then, apply the configuration with the command `kubectl apply -f role.yaml`.
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups:
        - ""
        - apps
        - autoscaling
        - batch
        - extensions
        - policy
        - rbac.authorization.k8s.io
    resources:
      - pods
      - secrets
      - componentstatuses
      - configmaps
      - daemonsets
      - deployments
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - pods
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - replicasets
      - replicationcontrollers
      - serviceaccounts
      - services
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```

### Bind the role to service account

role-bind.yaml
kubectl apply -f role-bind.yaml
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: webapps 
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: app-role 
subjects:
- namespace: webapps 
  kind: ServiceAccount
  name: jenkins 
```

### create secret for jenkins user
Note: kubectl apply -f secret.yaml -n webapps
secret.yaml
```
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: mysecretname
  annotations:
    kubernetes.io/service-account.name: jenkins

```
### copy the token using below command and add into jenkins global credentials -> secret text
```
root@ip-172-31-21-92:~# kubectl describe secret/mysecretname -n webapps
Name:         mysecretname
Namespace:    webapps
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: jenkins
              kubernetes.io/service-account.uid: 2ab9f470-5f4e-45d4-9ef9-37e9779958eb

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1107 bytes
namespace:  7 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Il9FVkRfbnlVbDBXZS02Z3RVS1JaX3o5a3JSSV9iOFdfQ05lUTZDSGF0XzQifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJ3ZWJhcHBzIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6Im15c2VjcmV0bmFtZSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJqZW5raW5zIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiMmFiOWY0NzAtNWY0ZS00NWQ0LTllZjktMzdlOTc3OTk1OGViIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50OndlYmFwcHM6amVua2lucyJ9.a7MPYgpxrRilRjGrbBO0pTM_ABvbtDsLYBR2d6yw-Ssz56VGbr-brAmO0WIoGIVfGCb6z_sNqPiJg3t2N5Ob0eVo2HZhNlbQoUFzEmqDN0jVRctuDPX60jxWd08V-mKF09b-Y4X4p94saoEDd04H6ep1WeNO_ZIpcHZcBlDa99ZQb1lFKId81nUaNKh4xdRJxgMEmjnfsqBqN_6C7Oy7iWa9_ZhwtJAotQSiR30DPm419ir_x9Uww7wwe090o32umEUmZspmITqmKRO6TNrdJJ9MduJq2WrEkLiC-g8d1FPkgSHQPvE9-oFuzy_TZJQ90vB3oeTyZlshhFSQUFYnOQ

```

## KUBECTL

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

### Generate token using service account in the namespace

[Create Token](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#:~:text=To%20create%20a%20non%2Dexpiring,with%20that%20generated%20token%20data.)

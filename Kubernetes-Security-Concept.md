## 1. Authentication (Who Are You?)

**Goal:** Verify user/application identity.

### A. Authentication Methods

| Method               | Description                              | Example                                                             |
|----------------------|------------------------------------------|---------------------------------------------------------------------|
| Static Password File | Basic username/password (not recommended) | `--basic-auth-file=users.csv`                                       |
| X509 Client Certs    | TLS certificates for users                | `kubectl --client-certificate=user.crt --client-key=user.key`       |
| Bearer Tokens        | JWT tokens (used by ServiceAccounts)      | `Authorization: Bearer <token>`                                     |
| OpenID Connect (OIDC)| Integrate with Google/GitHub/Azure AD     | `kubectl config set-credentials oidc-user --auth-provider=oidc`     |

### B. ServiceAccounts

**Purpose:** Used by Pods to authenticate to the Kubernetes API server securely.

#### Create a ServiceAccount

```sh
kubectl create serviceaccount my-app-sa
```

## 2. Authorization (What Can You Do?)
**Goal:** Control access to Kubernetes resources after authentication.

### A. RBAC (Role-Based Access Control)

- **Role**: Grants permissions within a specific namespace.
- **ClusterRole**: Grants permissions across the entire cluster.

#### Example: Create a Role to Read Pods

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

### ✅ Example RoleBinding: Assign Role to a User

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: default
subjects:
- kind: User
  name: alice
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```
🔐 This allows user alice to read pods in the default namespace.


### B. ABAC (Attribute-Based Access Control)

> 🔸 ABAC is a legacy authorization mode and **rarely used**. RBAC is preferred in production.

- **ABAC** uses user-defined policies to allow/deny access based on attributes like user, resource, namespace, etc.
- Enabled by starting `kube-apiserver` with `--authorization-mode=ABAC`.

---

#### ✅ Example ABAC Policy (JSON)

```json
{
  "apiVersion": "abac.authorization.kubernetes.io/v1",
  "kind": "Policy",
  "spec": {
    "user": "alice",
    "namespace": "*",
    "resource": "pods",
    "readonly": true
  }
}
```

### 3. Secrets Management
> Goal: Protect sensitive data (passwords, tokens).

#### A. Kubernetes Secrets
- Store secrets in **base64** (not encrypted by default).
- Secrets are used to pass sensitive data to Pods, such as passwords or API tokens.

---

#### ✅ Example:

```sh
kubectl create secret generic db-creds \
  --from-literal=username=admin \
  --from-literal=password='S3cr3t!'
```
#### Use in a Pod:
You can inject the secret into a Pod as an environment variable using `secretKeyRef`.

```yaml
env:
- name: DB_PASSWORD
  valueFrom:
    secretKeyRef:
      name: db-creds
      key: password
```

### B. External Secret Managers
AWS Secrets Manager, HashiCorp Vault, SealedSecrets (encrypts secrets for Git).

Example (SealedSecrets):
To encrypt a secret for safe storage in Git, you can use kubeseal to create a SealedSecret.

```sh
kubeseal --format=yaml < secret.yaml > sealed-secret.yaml
```
📝 kubeseal creates a SealedSecret, which can safely be stored in Git and decrypted by the Kubernetes controller at runtime.

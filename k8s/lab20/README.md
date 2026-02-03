# Lab 20: Securing Kubernetes with RBAC and Service Accounts

## Objective

Secure access to a Kubernetes cluster using **RBAC (Role-Based Access Control)** and **Service Accounts**.
In this lab, we create a ServiceAccount for Jenkins, grant it read-only access to Pods, and validate the permissions.

---

## Prerequisites

* Running Kubernetes cluster (Minikube)
* `kubectl` configured and connected to the cluster
* Namespace `ivolve` already exists

Verify namespace:

```bash
kubectl get ns ivolve
```

---

## Step 1: Create ServiceAccount (jenkins-sa)

Create a ServiceAccount in the `ivolve` namespace.

```bash
kubectl create serviceaccount jenkins-sa -n ivolve
```

Verify:

```bash
kubectl get sa -n ivolve
```

---

## Step 2: Create a Secret and Retrieve the ServiceAccount Token

### Create Secret Manually

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: jenkins-sa-token
  namespace: ivolve
  annotations:
    kubernetes.io/service-account.name: jenkins-sa
type: kubernetes.io/service-account-token
```

Apply it:

```bash
kubectl apply -f jenkins-sa-secret.yaml
```

### Retrieve Token

```bash
kubectl get secret jenkins-sa-token -n ivolve -o jsonpath='{.data.token}' | base64 --decode
```

---

## Step 3: Create Role (pod-reader)

This Role grants **read-only** access (`get`, `list`) to Pods in the `ivolve` namespace.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: ivolve
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

Apply it:

```bash
kubectl apply -f pod-reader-role.yaml
```

---

## Step 4: Create RoleBinding

Bind the `pod-reader` Role to the `jenkins-sa` ServiceAccount.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: ivolve
subjects:
- kind: ServiceAccount
  name: jenkins-sa
  namespace: ivolve
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

Apply it:

```bash
kubectl apply -f pod-reader-binding.yaml
```

---

## Step 5: Validation (Access Testing)

### Allowed: List Pods

```bash
kubectl auth can-i list pods \
  --as=system:serviceaccount:ivolve:jenkins-sa \
  -n ivolve
```

Expected output:

```
yes
```

### Allowed: Get Pod

```bash
kubectl auth can-i get pods \
  --as=system:serviceaccount:ivolve:jenkins-sa \
  -n ivolve
```

Expected output:

```
yes
```

### Denied: Delete Pod

```bash
kubectl auth can-i delete pods \
  --as=system:serviceaccount:ivolve:jenkins-sa \
  -n ivolve
```

Expected output:

```
no
```

---

## Conclusion

* ServiceAccount `jenkins-sa` was successfully created.
* RBAC Role `pod-reader` restricts access to read-only Pod operations.
* RoleBinding correctly binds permissions to the ServiceAccount.
* Validation confirms **least-privilege access**.

This setup is commonly used to securely integrate Jenkins with Kubernetes.

---

## Author

**Mohamed Ahmed Mohamed Taha**


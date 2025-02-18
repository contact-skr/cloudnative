# Accessing Kubernetes Dashboard (UI) on Windows with Docker Desktop

To access the **Kubernetes Dashboard (UI)** on Windows when running **Kubernetes through Docker Desktop**, follow these steps:

## 1. Enable Kubernetes in Docker Desktop
1. Open **Docker Desktop**.
2. Go to **Settings** > **Kubernetes**.
3. Check **Enable Kubernetes** and apply changes.
4. Wait until Kubernetes is **running**.

## 2. Install Kubernetes Dashboard
Run the following command in **PowerShell** or **Command Prompt**:

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

## 3. Create a Service Account & Role Binding
Create a file named **`admin-user.yaml`** and add the following content:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

Apply the configuration:

```sh
kubectl apply -f admin-user.yaml
```

## 4. Get the Access Token
Retrieve the token to log in:

```sh
kubectl -n kubernetes-dashboard create token admin-user
```

Copy the generated **token** for login.

## 5. Start the Kubernetes Dashboard
Run:

```sh
kubectl proxy
```

This will start a local proxy at `http://127.0.0.1:8001`.

## 6. Access the Dashboard
Open the following URL in your browser:

```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

- Choose **Token** authentication.
- Paste the **token** from Step 4.
- Click **Sign In**.

## Troubleshooting
- If the Dashboard is missing, reinstall it:
  ```sh
  kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
  ```
- If access is denied, check RBAC permissions:
  ```sh
  kubectl get clusterrolebinding admin-user
  ```
- If the UI doesn’t load, check logs:
  ```sh
  kubectl logs -n kubernetes-dashboard -l k8s-app=kubernetes-dashboard
  ```

---

### ⚠️ **Note for Production Use**
This setup is for **local development**. In production:
- Use **Ingress with HTTPS** instead of `kubectl proxy`.
- Implement **RBAC policies** for secure access.
- Consider **Kubernetes API & monitoring tools** instead of the UI.

---
✅ **Access Kubernetes Dashboard on Windows via Docker Desktop!** 🚀

# Kubernetes Networking Explained Like You're 5 🍪

Imagine Kubernetes is a big toy city where:

- **Pods** = Houses (with toy people inside).
- **Nodes** = Streets (where houses are built).
- **Services** = Mailboxes (to send letters to houses).
- **Ingress** = Post Office (routes letters to the right mailbox).

---

## Let’s break it down!

### 1. Every Pod (House) Gets Its Own Address
Each Pod (house) has a unique IP (like a street address).

❗ Problem: If a Pod crashes, a new one gets a new IP (like moving houses).

✅ **Solution:** Use a **Service** (mailbox)!

---

### 2. Services (Mailboxes) Make Life Easier
A **Service** is like a **mailbox**:

- It has a **fixed name and IP** (e.g., `my-app-service`).
- Even if Pods (houses) change, the mailbox stays the same.

#### Types of Services (Mailboxes)

| Type         | Real-Life Example                                    | Command                                                   |
|--------------|------------------------------------------------------|------------------------------------------------------------|
| `ClusterIP`  | A mailbox inside the city (only locals can use it). | `kubectl expose deploy/my-app --port=80`                  |
| `NodePort`   | A mailbox with a big sign (accessible from outside).| `kubectl expose deploy/my-app --type=NodePort --port=80`  |
| `LoadBalancer` | A fancy mailbox with a guard (cloud providers like AWS/GCP add it). | `kubectl expose deploy/my-app --type=LoadBalancer --port=80` |

---

### 3. Ingress (Post Office) Routes Traffic

📦 **Problem:** Too many mailboxes! How do we send letters to the right one?

📬 **Solution:** Use **Ingress** – it's like the **Post Office**!  
It reads the address and delivers traffic to the correct service (mailbox).

---

#### 🍕 Example: Pizza Delivery

You want to order pizza at **pizza.com**.

The Ingress routes your requests like this:

- `pizza.com/order` → `pizza-service`
- `pizza.com/menu` → `menu-service`

---

#### 📝 YAML Example:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: pizza-ingress
spec:
  rules:
  - host: pizza.com
    http:
      paths:
      - path: /order
        pathType: Prefix
        backend:
          service:
            name: pizza-service
            port:
              number: 80
      - path: /menu
        pathType: Prefix
        backend:
          service:
            name: menu-service
            port:
              number: 80
```

### 4. Network Policies (Traffic Rules)

🚨 **Problem:** Bad guys are sending spam letters to our houses (Pods)!

🛡️ **Solution:** Use **Network Policies** — like hiring a **security guard**  
to control who can talk to whom in the toy city.

---

#### 🔒 Example:

Only **frontend Pods** are allowed to talk to **backend Pods**.  
Everyone else is blocked.

---

#### 📝 YAML Example:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

### 5. DNS (Phone Book for Services)

📞 **Problem:** It's hard to remember the exact IP address of each mailbox (Service)!

📚 **Solution:** Kubernetes gives every Service a **name**, just like a **phone book entry**.

---

#### 🔍 Example:

Instead of IPs, you use names like:

- `pizza-service.default.svc.cluster.local` → 🍕 The pizza mailbox  
- `db-service.default.svc.cluster.local` → 🗄️ The database mailbox  

---

🧠 **How it works:**  
Kubernetes runs a built-in DNS service called **CoreDNS** that keeps track of all Service names.

So whenever a Pod wants to talk to another Service, it can simply **call the name**,  
and CoreDNS will connect it to the right address — even if that address changes!

📖 Like calling "Pizza Store" instead of remembering the exact number!


### 6. CNI (The Road Builders) 🛣️

🚧 **Problem:**  
How can Pods (houses) on different streets (nodes) talk to each other?

🛠️ **Solution:**  
**CNI plugins** like **Calico**, **Flannel**, and **Weave** are like **road construction crews** —  
they **build and maintain the roads** between all the houses (Pods) across all the streets (nodes).

---

#### 🧱 Real-Life Analogy:

- Without roads: Houses on different streets can't deliver mail or visit each other.
- With CNI: Every house is connected via a road, so Pods on one node can talk to Pods on another.

---

### 🧩 Summary Table

| **Concept**      | **Real-Life Example** | **Purpose**                          |
|------------------|------------------------|--------------------------------------|
| Pod              | 🏠 House               | Runs your app                        |
| Service          | 📬 Mailbox            | Stable address for Pods              |
| Ingress          | 🏤 Post Office        | Routes HTTP traffic                  |
| NetworkPolicy    | 🛡️ Security Guard    | Blocks or allows traffic             |
| DNS              | ☎️ Phone Book         | Finds Services by name               |
| CNI              | 🛣️ Road Builder


# Kubernetes Networking Commands Cheat Sheet
All essential commands with explanations and arguments.

## 1. Basic Networking Inspection

### List all Pods with IPs
```sh
kubectl get pods -o wide
```
-o wide: Shows Node and IP details.

Use case: Check which Pod runs where and its IP.

## Describe a Pod (See Networking Details)
```sh
kubectl describe pod <pod-name>
```
Shows:
- IP address
- Node it’s running on
- Events (e.g., failed scheduling due to network issues)

## 2. Service Management

### Create a ClusterIP Service
```sh
kubectl expose deployment/my-app --port=80 --target-port=8080 --name=my-service
```
--port: Service port (e.g., 80).

--target-port: Pod port (e.g., 8080).

--name: Service name.

### Create a NodePort Service
```sh
kubectl expose deployment/my-app --type=NodePort --port=80 --target-port=8080
```
--type=NodePort: Exposes on a static port (30000-32767).

### Check NodePort

```
kubectl get svc my-app
```
→ Output: 80:32456/TCP means port 32456 is open on every Node.

### Create a LoadBalancer (Cloud Providers Only)
```sh
kubectl expose deployment/my-app --type=LoadBalancer --port=80
```
Cloud providers (AWS/GCP/Azure) assign an external IP.

### Check with
```sh
kubectl get svc
```
→ EXTERNAL-IP appears after provisioning.

### Delete a Service

```sh
kubectl delete svc my-service
```

## 3. Ingress Commands

### Apply an Ingress Rule
```sh
kubectl apply -f ingress.yaml
```

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
  - host: myapp.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-service
            port:
              number: 80
```
### List All Ingresses
```sh
kubectl get ingress
```
Shows:

- HOST (myapp.com)
- ADDRESS (IP of the Ingress Controller)

### Delete an Ingress

```sh
kubectl delete ingress my-ingress
```

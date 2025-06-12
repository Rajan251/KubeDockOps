# Install Kubernetes Dashboard

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
# Create admin user
cat <<EOF | kubectl apply -f -
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
EOF
```

# Get dashboard token
```
kubectl -n kubernetes-dashboard create token admin-user
```

Great! You've successfully installed the Kubernetes Dashboard. Now let me show you how to access it and what to do with the token.

## Access the Dashboard

After running those commands, you need to start a proxy to access the dashboard:

```bash
# Start the kubectl proxy (this will run in foreground)
kubectl proxy
```

Once the proxy is running, you can access the dashboard at:
```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

## Alternative: Port Forward Method

If you prefer, you can also use port forwarding instead of proxy:

```bash
# Forward dashboard port to your local machine
kubectl port-forward -n kubernetes-dashboard service/kubernetes-dashboard 8443:443
```

Then access it at: `https://localhost:8443`

## Using the Token

1. When you visit the dashboard URL, you'll see a login screen
2. Select "Token" as the authentication method
3. Paste the token you got from the `kubectl -n kubernetes-dashboard create token admin-user` command
4. Click "Sign In"

## Get Token Anytime

If you need to get the token again later:

```bash
kubectl -n kubernetes-dashboard create token admin-user
```

## Dashboard Features

Once logged in, you can:
- View all your workloads (pods, deployments, services)
- Monitor resource usage
- Check logs
- Deploy applications through the UI
- Manage secrets and config maps
- Scale deployments

## Security Note

The admin-user you created has full cluster admin privileges. For production environments, you'd want to create users with more limited permissions.

## Troubleshooting

If you can't access the dashboard:
1. Make sure `kubectl proxy` is running
2. Check if dashboard pods are running: `kubectl get pods -n kubernetes-dashboard`
3. Verify the service is available: `kubectl get svc -n kubernetes-dashboard`

Your dashboard should now be fully functional and ready to use!

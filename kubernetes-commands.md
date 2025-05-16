# Ultimate Kubernetes Command Reference Guide

This comprehensive guide organizes kubectl commands by skill level from beginner to expert, making it easy to find commands appropriate for your experience level.

## Basic Command Structure

```
kubectl [command] [TYPE] [NAME] [flags]
```

## Level 1: Essential Commands (Beginner)

### Resource Viewing
- `kubectl create -f FILE` - Create from file
- `kubectl create -f DIR/` - Create from all files in directory
- `kubectl create -f URL` - Create from URL
- `kubectl create deployment nginx --image=nginx` - Create deployment
- `kubectl create job my-job --image=busybox -- date` - Create job with command
- `kubectl create namespace development` - Create namespace
- `kubectl create sa service-account` - Create service account
- `kubectl create secret generic my-secret --from-literal=key=value` - Create secret from literal
- `kubectl create secret generic my-secret --from-file=path/to/file` - Create secret from file
- `kubectl create secret tls tls-secret --cert=path/to/cert --key=path/to/key` - Create TLS secret
- `kubectl create configmap my-config --from-literal=key=value` - Create configmap from literal
- `kubectl create configmap my-config --from-file=path/to/file` - Create configmap from file
- `kubectl create configmap my-config --from-env-file=path/to/env.file` - Create configmap from env file
- `kubectl create role pod-reader --verb=get,list,watch --resource=pods` - Create role
- `kubectl create rolebinding bob-reader --role=pod-reader --user=bob` - Create rolebinding
- `kubectl create clusterrole pod-reader --verb=get,list,watch --resource=pods` - Create clusterrole
- `kubectl create clusterrolebinding bob-reader --clusterrole=pod-reader --user=bob` - Create clusterrolebinding
- `kubectl create pdb my-pdb --selector=app=nginx --min-available=2` - Create pod disruption budget
- `kubectl create priorityclass high-priority --value=1000 --description="High priority"` - Create priority class
- `kubectl create quota my-quota --hard=cpu=1,memory=1G,pods=2` - Create resource quota
- `kubectl create serviceaccount my-sa` - Create service account
- `kubectl create token my-sa` - Create token for service account

### Get Resources
- `kubectl get pods` - List all pods in current namespace
- `kubectl get nodes` - List all nodes in the cluster
- `kubectl get services` - List all services
- `kubectl get deployments` - List all deployments
- `kubectl get namespaces` - List all namespaces
- `kubectl get all` - List all resources in the current namespace
- `kubectl describe pod [pod-name]` - View detailed information about a pod
- `kubectl describe node [node-name]` - View detailed information about a node
- `kubectl logs [pod-name]` - View logs for a pod
- `kubectl logs -f [pod-name]` - Stream logs from a pod

### Basic Resource Management
- `kubectl create -f [filename.yaml]` - Create resource from file
- `kubectl apply -f [filename.yaml]` - Create or update resource from file
- `kubectl delete -f [filename.yaml]` - Delete resource from file
- `kubectl delete pod [pod-name]` - Delete a specific pod
- `kubectl run nginx --image=nginx` - Create a deployment running nginx
- `kubectl expose deployment [deployment-name] --port=80 --type=ClusterIP` - Expose a deployment as a service

### Configuration and Context
- `kubectl config get-contexts` - List available contexts
- `kubectl config current-context` - Show current context
- `kubectl config use-context [context-name]` - Switch to another context
- `kubectl config view` - Show merged kubeconfig settings

### Help and Information
- `kubectl version` - Display client and server versions
- `kubectl cluster-info` - Display cluster information
- `kubectl explain pods` - Get documentation for pods
- `kubectl api-resources` - List all resource types
- `kubectl help` - Display help information

## Level 2: Intermediate Commands

### Advanced Resource Viewing
- `kubectl get pods -o wide` - List pods with more details
- `kubectl get pods --all-namespaces` - List pods in all namespaces
- `kubectl get pods -l app=nginx` - List pods with specific label
- `kubectl get pods --field-selector=status.phase=Running` - List running pods
- `kubectl get pods --sort-by='.metadata.creationTimestamp'` - Sort pods by creation time
- `kubectl get pods -o yaml` - Get pod details in YAML format
- `kubectl get pods -o json` - Get pod details in JSON format
- `kubectl get pods -w` - Watch pods for changes

### Advanced Pod Management
- `kubectl exec -it [pod-name] -- /bin/bash` - Execute interactive shell in pod
- `kubectl exec [pod-name] -- [command]` - Run command in pod
- `kubectl cp [pod-name]:/path/to/file /local/path` - Copy files from pod to local
- `kubectl cp /local/path [pod-name]:/path/to/file` - Copy files from local to pod
- `kubectl port-forward [pod-name] 8080:80` - Forward local port to pod port
- `kubectl logs [pod-name] -c [container-name]` - View logs for specific container in pod
- `kubectl logs [pod-name] --previous` - View logs from previous container instance

### Deployment Operations
- `kubectl scale deployment [deployment-name] --replicas=3` - Scale a deployment
- `kubectl rollout status deployment/[deployment-name]` - Check rollout status
- `kubectl rollout history deployment/[deployment-name]` - View rollout history
- `kubectl rollout undo deployment/[deployment-name]` - Rollback to previous version
- `kubectl set image deployment/[deployment-name] [container]=[image]:[tag]` - Update container image
- `kubectl edit deployment [deployment-name]` - Edit deployment configuration

### Configuration Management
- `kubectl create configmap [name] --from-file=[path]` - Create ConfigMap from file
- `kubectl create secret generic [name] --from-literal=key=value` - Create Secret from literal
- `kubectl create namespace [name]` - Create namespace
- `kubectl config set-context --current --namespace=[namespace]` - Change default namespace

## Level 3: Advanced Commands

### Resource Management and Updates
- `kubectl apply -f [directory]/` - Apply configuration from all files in directory
- `kubectl apply -f [file] --record` - Record current command in resource annotation
- `kubectl replace --force -f [file]` - Force replace resource from file
- `kubectl patch deployment [name] -p '{"spec":{"replicas":2}}'` - Patch resource using JSON
- `kubectl patch deployment [name] --type=json -p='[{"op":"replace","path":"/spec/replicas","value":2}]'` - JSON patch
- `kubectl create job [name] --image=[image] -- [command]` - Create job with command
- `kubectl create cronjob [name] --image=[image] --schedule="*/1 * * * *" -- [command]` - Create cronjob

### RBAC and Security
- `kubectl create serviceaccount [name]` - Create service account
- `kubectl create role [name] --verb=get,list,watch --resource=pods` - Create role
- `kubectl create rolebinding [name] --role=[role] --user=[user]` - Create role binding
- `kubectl create clusterrole [name] --verb=get,list,watch --resource=pods` - Create cluster role
- `kubectl create clusterrolebinding [name] --clusterrole=[role] --user=[user]` - Create cluster role binding
- `kubectl auth can-i create deployments` - Check if you can create deployments
- `kubectl auth can-i list pods --as=[user]` - Check permissions for another user

### Node Management
- `kubectl cordon [node-name]` - Mark node as unschedulable
- `kubectl uncordon [node-name]` - Mark node as schedulable
- `kubectl drain [node-name]` - Drain node in preparation for maintenance
- `kubectl drain [node-name] --ignore-daemonsets` - Drain node ignoring daemonsets
- `kubectl taint nodes [node-name] key=value:NoSchedule` - Add taint to node

### Networking
- `kubectl port-forward svc/[service-name] 8080:80` - Forward local port to service port
- `kubectl expose deployment [name] --type=LoadBalancer --port=80 --target-port=8080` - Create LoadBalancer service
- `kubectl create ingress [name] --rule="host/path=service:port"` - Create ingress
- `kubectl get networkpolicies` - List network policies

## Level 4: Expert Commands and Techniques

### Advanced Troubleshooting
- `kubectl debug [pod-name] -it --image=busybox` - Debug with ephemeral container
- `kubectl debug node/[node-name] -it --image=ubuntu` - Debug node using pod
- `kubectl run test --image=busybox --rm -it -- sh` - Run temporary debugging pod
- `kubectl alpha debug [pod-name] --copy-to=[name] --container=[container]` - Debug by creating copy
- `kubectl get events --sort-by=.metadata.creationTimestamp` - Get events in chronological order
- `kubectl get pods --field-selector=status.phase=Failed` - List failed pods

### Resource Queries and Filtering
- `kubectl get pods -o jsonpath='{.items[*].metadata.name}'` - Extract pod names
- `kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="InternalIP")].address}'` - Get node IPs
- `kubectl get pods -o=custom-columns=NAME:.metadata.name,STATUS:.status.phase,NODE:.spec.nodeName` - Custom columns
- `kubectl get pods --field-selector=spec.nodeName=[node-name],status.phase=Running` - Multiple field selectors
- `kubectl get pods -A -o custom-columns="NAMESPACE:.metadata.namespace,POD:.metadata.name,PVC:.spec.volumes[*].persistentVolumeClaim.claimName"` - Find pods using PVCs

### Advanced Configuration Management
- `kubectl kustomize ./overlay/production | kubectl apply -f -` - Apply kustomized resources
- `kubectl diff -f [file]` - View differences before applying
- `kubectl rollout pause deployment/[name]` - Pause deployment rollout
- `kubectl rollout resume deployment/[name]` - Resume deployment rollout
- `kubectl rollout restart deployment/[name]` - Restart deployment (trigger rolling update)
- `kubectl wait --for=condition=available deployment/[name] --timeout=90s` - Wait for condition
- `kubectl auth reconcile -f rbac.yaml` - Reconcile RBAC resources

### Cluster Administration
- `kubectl drain [node-name] --pod-selector="app!=critical"` - Drain with pod selector
- `kubectl top node | sort -k 3 -h` - Sort nodes by CPU usage
- `kubectl get apiservices` - List API services
- `kubectl get crd` - List custom resource definitions
- `kubectl api-resources --verbs=list,get` - List resources supporting specific verbs
- `kubectl cluster-info dump` - Dump cluster state for debugging
- `kubectl get componentstatuses` - Get component statuses

### Advanced Batch Operations
- `kubectl get pods -l app=web --no-headers | awk '{print $1}' | xargs kubectl delete pod` - Delete pods by label
- `kubectl get nodes -o jsonpath='{.items[*].metadata.name}' | xargs -I{} kubectl drain {} --ignore-daemonsets` - Drain all nodes
- `kubectl get ns -o jsonpath='{.items[*].metadata.name}' | xargs -I{} kubectl get pods -n {}` - List pods in all namespaces
- `kubectl get secrets --all-namespaces -o json | jq -r '.items[] | select(.type=="kubernetes.io/service-account-token") | .metadata.name'` - List service account tokens

## Level 5: Kubernetes Power Tools and Patterns

### Custom Resource Management
- `kubectl get prometheuses -n monitoring` - Get custom resources (e.g., Prometheus)
- `kubectl describe crd prometheuses.monitoring.coreos.com` - Describe custom resource definition
- `kubectl api-resources --api-group=monitoring.coreos.com` - List resources in API group
- `kubectl explain prometheuses.spec` - Get documentation for custom resource

### Advanced Configuration Techniques
- `kubectl config view --flatten > merged_kubeconfig` - Export flattened kubeconfig
- `kubectl config set-credentials user --client-certificate=path/to/cert --client-key=path/to/key` - Set user credentials
- `kubectl config set-cluster prod --server=https://kube-prod.example.com` - Add new cluster
- `kubectl config set-context prod --cluster=prod --user=admin --namespace=prod` - Create context
- `kubectl config use-context $(kubectl config get-contexts -o name | fzf)` - Interactive context selection

### Dynamic Resource Manipulation
- `kubectl get pod -o yaml | sed 's/memory: 100Mi/memory: 200Mi/' | kubectl replace -f -` - Edit and replace in one line
- `kubectl get configmap -o json | jq '.items[].data' | grep password` - Filter and search ConfigMaps
- `kubectl get deployment -o json | jq 'del(.items[].metadata.resourceVersion)' | kubectl apply -f -` - Apply without resource version
- `kubectl create secret generic creds --from-literal=user=admin --dry-run=client -o yaml | kubectl apply -f -` - Generate and apply
- `kubectl patch storageclass standard -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'` - Set default storage class

### Advanced Monitoring and Troubleshooting
- `kubectl top pod --containers=true` - Show resource usage by container
- `kubectl exec -it deploy/database -- mysql -u root -p` - Connect to database in pod
- `kubectl logs -l app=nginx --all-containers=true --since=1h` - Get logs from all containers matching label
- `kubectl describe pod [pod-name] | grep -A10 Events` - View recent events for pod
- `kubectl get pod [pod-name] -o yaml | kubectl neat | grep -i readiness` - Clean output and filter

### Infrastructure and Automation
- `kubectl drain --selector=node-role.kubernetes.io/master --ignore-daemonsets` - Drain all master nodes
- `kubectl cordon $(kubectl get nodes -l role=worker -o name)` - Cordon all worker nodes
- `kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.capacity.cpu}{"\t"}{.status.capacity.memory}{"\n"}{end}'` - Node capacity report
- `kubectl create job test-job --from=cronjob/backup-job` - Create job from cronjob
- `kubectl convert -f deployment-old.yaml --output-version apps/v1` - Convert between API versions

### Delete Resources
- `kubectl delete -f FILE` - Delete from file
- `kubectl delete -f DIR/` - Delete from all files in directory
- `kubectl delete -f URL` - Delete from URL
- `kubectl delete pod mypod` - Delete pod
- `kubectl delete pod mypod --now` - Delete pod immediately
- `kubectl delete pod mypod --grace-period=0 --force` - Force delete pod
- `kubectl delete pods --all` - Delete all pods in current namespace
- `kubectl delete pods -l app=nginx` - Delete pods with specific label
- `kubectl delete pods -n kube-system --field-selector status.phase=Failed` - Delete failed pods
- `kubectl delete pods,services -l app=nginx` - Delete multiple resource types with label
- `kubectl delete namespaces development` - Delete namespace and all contained resources
- `kubectl delete all --all` - Delete all resources in current namespace
- `kubectl delete all -l app=nginx` - Delete all resources with label
- `kubectl delete all -l app=nginx --cascade=foreground` - Delete with foreground cascading
- `kubectl delete all -l app=nginx --cascade=orphan` - Delete without affecting dependent objects

### Apply Configuration
- `kubectl apply -f FILE` - Apply from file
- `kubectl apply -f DIR/` - Apply from all files in directory
- `kubectl apply -f URL` - Apply from URL
- `kubectl apply -f FILE --record` - Apply with recording in rollout history
- `kubectl apply -f FILE --prune` - Delete resources not in file
- `kubectl apply -f FILE --prune -l app=nginx` - Delete resources with specific label not in file
- `kubectl apply -f FILE --server-side` - Apply server side
- `kubectl apply -f FILE --dry-run=client` - Client-side dry run
- `kubectl apply -f FILE --dry-run=server` - Server-side dry run
- `kubectl apply -f FILE --force` - Force apply, discard and recreate resources if needed
- `kubectl apply -f FILE -l app=nginx` - Apply only resources with specific label
- `kubectl apply -f FILE --overwrite=false` - Apply without overwriting existing fields
- `kubectl apply -k ./dir` - Apply from kustomization directory
- `kubectl apply view-last-applied pod/mypod` - View last applied configuration
- `kubectl apply set-last-applied -f FILE` - Set last applied configuration

### Resource Editing
- `kubectl edit pod mypod` - Edit pod in default editor
- `kubectl edit service myservice` - Edit service
- `kubectl edit deployment mydeployment` - Edit deployment
- `kubectl edit cm myconfig` - Edit configmap
- `kubectl edit deploy mydeployment -o yaml` - Edit in YAML format
- `kubectl edit svc myservice --save-config` - Edit and save config annotation
- `kubectl edit deployment mydeployment --record` - Edit with recording in history
- `kubectl edit deployment/mydeployment --windows-line-endings` - Edit with Windows line endings

### Update Resources
- `kubectl set image deployment/myapp container-name=new-image:tag` - Update image
- `kubectl set image daemonset/myds container-name=new-image:tag` - Update daemonset image
- `kubectl set image statefulset/mysts container-name=new-image:tag` - Update statefulset image
- `kubectl set resources deployment/myapp -c=container-name --limits=cpu=200m,memory=512Mi` - Set resource limits
- `kubectl set resources deployment/myapp -c=container-name --requests=cpu=100m,memory=256Mi` - Set resource requests
- `kubectl set env deployment/myapp KEY=VALUE` - Set environment variable
- `kubectl set env deployment/myapp KEY-` - Remove environment variable
- `kubectl set env deployment/myapp --from=configmap/mycm` - Set env from configmap
- `kubectl set env deployment/myapp --from=secret/mysecret` - Set env from secret
- `kubectl set selector service myservice 'app=myapp,tier=frontend'` - Set service selector
- `kubectl set serviceaccount deployment myapp my-sa` - Set service account
- `kubectl set subject rolebinding admin --user=user1` - Set role binding user
- `kubectl set subject clusterrolebinding cluster-admin --group=system:masters` - Set cluster role binding group

### Patch Resources
- `kubectl patch pod mypod -p '{"spec":{"containers":[{"name":"container-name","image":"new-image"}]}}'` - Patch pod using JSON
- `kubectl patch deployment myapp --patch-file patch-file.yaml` - Patch from file
- `kubectl patch service myservice -p '{"spec":{"type":"LoadBalancer"}}'` - Patch service type
- `kubectl patch node minikube -p '{"spec":{"unschedulable":true}}'` - Mark node unschedulable
- `kubectl patch pod mypod --type=json -p='[{"op":"replace","path":"/spec/containers/0/image","value":"new-image"}]'` - JSON patch
- `kubectl patch pod mypod --type=merge -p '{"spec":{"containers":[{"name":"container-name","image":"new-image"}]}}'` - Merge patch
- `kubectl patch pod mypod --type=strategic -p '{"spec":{"containers":[{"name":"container-name","image":"new-image"}]}}'` - Strategic merge patch

### Describe Resources
- `kubectl describe pod mypod` - Describe pod
- `kubectl describe node minikube` - Describe node
- `kubectl describe deployment mydeployment` - Describe deployment
- `kubectl describe service myservice` - Describe service
- `kubectl describe pv mypv` - Describe persistent volume
- `kubectl describe configmap mycm` - Describe configmap
- `kubectl describe secret mysecret` - Describe secret
- `kubectl describe pvc mypvc` - Describe persistent volume claim
- `kubectl describe ingress myingress` - Describe ingress
- `kubectl describe hpa myhpa` - Describe horizontal pod autoscaler
- `kubectl describe rs myrs` - Describe replica set
- `kubectl describe sts mysts` - Describe stateful set
- `kubectl describe ds myds` - Describe daemon set
- `kubectl describe job myjob` - Describe job
- `kubectl describe cronjob mycj` - Describe cron job
- `kubectl describe networkpolicy mynp` - Describe network policy

### Resource Scaling
- `kubectl scale deployment myapp --replicas=3` - Scale deployment to 3 replicas
- `kubectl scale statefulset mysts --replicas=5` - Scale stateful set
- `kubectl scale replicaset myrs --replicas=0` - Scale replica set to zero
- `kubectl scale deployment myapp --replicas=3 --record` - Scale with recording history
- `kubectl scale --replicas=3 -f deployment.yaml` - Scale using file definition
- `kubectl scale deployment myapp --current-replicas=2 --replicas=3` - Scale only if current size matches
- `kubectl scale deployments,statefulsets --all --replicas=0` - Scale multiple resource types to zero
- `kubectl scale deployment myapp --replicas=5 --timeout=1m` - Scale with timeout

## Pod Management

### Pod Operations
- `kubectl run nginx --image=nginx` - Create and run a pod
- `kubectl run nginx --image=nginx --restart=Never` - Create a pod without a deployment
- `kubectl run nginx --image=nginx --restart=OnFailure` - Create a job
- `kubectl run nginx --image=nginx --restart=OnFailure --schedule="*/1 * * * *"` - Create a cronjob
- `kubectl run nginx --image=nginx --port=80 --expose` - Create pod and service
- `kubectl run debug --image=busybox --rm -it --restart=Never -- sh` - Run temporary pod for debugging
- `kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml` - Generate pod YAML
- `kubectl run nginx --image=nginx --labels="app=web,env=prod"` - Run pod with labels
- `kubectl run nginx --image=nginx --env="DB_HOST=postgres"` - Run pod with environment variables
- `kubectl run nginx --image=nginx --serviceaccount=mysa` - Run pod with specific service account
- `kubectl run nginx --image=nginx --limits="cpu=200m,memory=512Mi"` - Run pod with resource limits
- `kubectl run nginx --image=nginx --requests="cpu=100m,memory=256Mi"` - Run pod with resource requests
- `kubectl run nginx --image=nginx --command -- /bin/sh -c "while true; do echo hello; sleep 10; done"` - Run pod with custom command
- `kubectl run nginx --image=nginx --overrides='{"spec":{"nodeSelector":{"disktype":"ssd"}}}'` - Run pod with overrides
- `kubectl attach mypod -i` - Attach to pod
- `kubectl attach mypod -c container-name -i -t` - Attach to specific container with TTY
- `kubectl port-forward pod/mypod 8080:80` - Forward local port to pod port
- `kubectl port-forward svc/myservice 8080:80` - Forward local port to service port
- `kubectl port-forward deployment/mydeployment 8080:80` - Forward to deployment port
- `kubectl exec mypod -- ls /` - Execute command in pod
- `kubectl exec -it mypod -- bash` - Execute interactive shell in pod
- `kubectl exec -it mypod -c container-name -- bash` - Execute shell in specific container
- `kubectl exec mypod -- env | grep PATH` - Pipe and grep command output
- `kubectl cp /local/path mypod:/container/path` - Copy local file to pod
- `kubectl cp mypod:/container/path /local/path` - Copy from pod to local
- `kubectl cp /local/path mypod:/container/path -c container-name` - Copy to specific container
- `kubectl logs mypod` - Show pod logs
- `kubectl logs -f mypod` - Stream pod logs
- `kubectl logs mypod -c container-name` - Show specific container logs
- `kubectl logs mypod --tail=20` - Show last 20 lines of logs
- `kubectl logs mypod --since=1h` - Show logs from last hour
- `kubectl logs mypod --timestamps` - Show logs with timestamps
- `kubectl logs -l app=nginx` - Show logs for pods with label
- `kubectl logs --previous mypod` - Show logs from previous container instance
- `kubectl logs -f deployment/myapp` - Stream logs from deployment
- `kubectl logs job/myjob` - Show job logs
- `kubectl logs --all-containers=true mypod` - Show logs from all containers in pod
- `kubectl logs mypod --container=container-name --follow` - Follow specific container
- `kubectl logs mypod --since-time="2023-01-01T00:00:00Z"` - Logs since specific time

### Debugging and Troubleshooting Pods
- `kubectl debug node/mynode -it --image=ubuntu` - Debug node using pod
- `kubectl debug mypod -it --copy-to=pod-debug --container=container-name` - Debug by creating copy
- `kubectl debug mypod -it --container=container-name -- sh` - Start debug container
- `kubectl debug deployment/myapp --copy-to=app-debug --set-image=*=ubuntu` - Debug with different image
- `kubectl debug mypod --profile=legacy-profile` - Debug with profile
- `kubectl debug mypod --share-processes` - Debug with shared process namespace
- `kubectl debug mypod -it --image=ubuntu --share-processes` - Add debug container with process sharing
- `kubectl alpha debug mypod --image=busybox --target=container-name` - Debug specific container (alpha)
- `kubectl replace --force -f pod.yaml` - Force recreate pod
- `kubectl get pods mypod -o yaml | kubectl replace --force -f -` - Force restart pod

## Deployment Management

### Deployment Operations
- `kubectl rollout status deployment/myapp` - Check rollout status
- `kubectl rollout history deployment/myapp` - View rollout history
- `kubectl rollout history deployment/myapp --revision=2` - View specific revision
- `kubectl rollout undo deployment/myapp` - Rollback to previous version
- `kubectl rollout undo deployment/myapp --to-revision=2` - Rollback to specific revision
- `kubectl rollout restart deployment/myapp` - Restart deployment (trigger rolling update)
- `kubectl rollout pause deployment/myapp` - Pause rollout
- `kubectl rollout resume deployment/myapp` - Resume rollout
- `kubectl set image deployment/myapp mycontainer=nginx:1.9.1 --record` - Update image
- `kubectl set image deployment/myapp *=nginx:1.9.1` - Update all containers in deployment
- `kubectl rollout status daemonset/myds` - Check daemonset rollout
- `kubectl rollout history statefulset/mysts` - View statefulset history
- `kubectl wait --for=condition=available deployment/myapp --timeout=90s` - Wait for deployment to be available
- `kubectl create deployment myapp --image=nginx:1.14.2` - Create deployment
- `kubectl create deployment myapp --image=nginx:1.14.2 --replicas=3` - Create deployment with replicas
- `kubectl expose deployment myapp --port=80 --target-port=8080` - Expose deployment as service
- `kubectl expose deployment myapp --type=LoadBalancer --port=80` - Expose as load balancer
- `kubectl autoscale deployment myapp --min=2 --max=5 --cpu-percent=80` - Autoscale deployment

## Service and Networking

### Services
- `kubectl get svc` - List services
- `kubectl get endpoints` - List endpoints
- `kubectl get ingress` - List ingresses
- `kubectl expose pod mypod --port=80 --name=pod-service` - Create service for pod
- `kubectl expose rc myrc --port=80 --target-port=8000` - Expose replica controller
- `kubectl expose deployment myapp --type=NodePort --port=80 --name=myapp-service` - Create NodePort service
- `kubectl expose deployment myapp --type=LoadBalancer --port=80` - Create LoadBalancer service
- `kubectl expose deployment myapp --type=ExternalName --external-name=my.database.example.com` - ExternalName service
- `kubectl expose deployment myapp --port=80 --target-port=8080 --name=myapp-service --type=ClusterIP` - ClusterIP service
- `kubectl get service myservice -o jsonpath='{.spec.clusterIP}'` - Get service cluster IP
- `kubectl get service myservice -o jsonpath='{.spec.ports[0].nodePort}'` - Get NodePort
- `kubectl port-forward svc/redis-master 6379:6379` - Forward to service
- `kubectl port-forward pods/mypod 8080:80 8443:443` - Forward multiple ports

### Network Policies
- `kubectl get networkpolicies` - List network policies
- `kubectl describe networkpolicy mynetworkpolicy` - Describe network policy
- `kubectl create networkpolicy my-policy --pod-selector=app=db --ingress-rule="app=web" --port=5432` - Create network policy

### Ingress
- `kubectl get ingress` - List ingresses
- `kubectl describe ingress myingress` - Describe ingress
- `kubectl create ingress myingress --rule="foo.com/path*=service:port"` - Create ingress
- `kubectl create ingress myingress --class=nginx --rule="foo.com/path*=service:port"` - Create with ingress class
- `kubectl create ingress myingress --rule="foo.com/path*=service:port,tls=secret"` - Create with TLS

## Configuration and Storage

### ConfigMaps
- `kubectl create configmap mycm --from-literal=key1=value1` - Create configmap from literal
- `kubectl create configmap mycm --from-file=config.properties` - Create from file
- `kubectl create configmap mycm --from-env-file=env.txt` - Create from env file
- `kubectl create configmap mycm --from-file=key1=config1.properties --from-file=key2=config2.properties` - Multiple files
- `kubectl get configmap mycm -o yaml` - Get configmap in YAML
- `kubectl describe configmap mycm` - Describe configmap
- `kubectl edit configmap mycm` - Edit configmap

### Secrets
## Shortcuts and Aliases

For faster command execution, Kubernetes provides many resource type shortcuts:

```
pods                    → po
services                → svc
deployments             → deploy
replicasets             → rs
replicationcontrollers  → rc
nodes                   → no
namespaces              → ns
persistentvolumes       → pv
persistentvolumeclaims  → pvc
configmaps              → cm
endpoints               → ep
events                  → ev
ingresses               → ing
customresourcedefinition→ crd
storageclasses          → sc
statefulsets            → sts
daemonsets              → ds
horizontalpodautoscalers→ hpa
serviceaccounts         → sa
cronjobs                → cj
```

## Common Command Patterns by Use Case

### Application Deployment
1. Create namespace: `kubectl create namespace my-app`
2. Apply configuration: `kubectl apply -f my-app.yaml -n my-app`
3. Verify deployment: `kubectl get deployment -n my-app`
4. Check pods: `kubectl get pods -n my-app`
5. View logs: `kubectl logs -l app=my-app -n my-app`
6. Expose service: `kubectl expose deployment my-app --port=80 --type=LoadBalancer -n my-app`

### Troubleshooting Workflow
1. Check pod status: `kubectl get pods -n my-app`
2. Describe problematic pod: `kubectl describe pod [pod-name] -n my-app`
3. Check logs: `kubectl logs [pod-name] -n my-app`
4. Check previous container logs (if crashed): `kubectl logs [pod-name] --previous -n my-app`
5. Execute into container: `kubectl exec -it [pod-name] -n my-app -- /bin/bash`
6. Check events: `kubectl get events -n my-app --sort-by='.lastTimestamp'`

### Node Maintenance
1. Mark node as unschedulable: `kubectl cordon [node-name]`
2. Drain applications: `kubectl drain [node-name] --ignore-daemonsets`
3. Perform maintenance
4. Mark node as schedulable: `kubectl uncordon [node-name]`
5. Verify node status: `kubectl describe node [node-name]`

### Configuration Updates
1. View current config: `kubectl get deployment [name] -o yaml`
2. Update config: `kubectl edit deployment [name]` or `kubectl apply -f updated-config.yaml`
3. Check rollout status: `kubectl rollout status deployment/[name]`
4. View rollout history: `kubectl rollout history deployment/[name]`
5. Rollback if needed: `kubectl rollout undo deployment/[name]`

### Persistent Volumes
- `kubectl get pv` - List persistent volumes
- `kubectl get pvc` - List persistent volume claims
- `kubectl describe pv mypv` - Describe persistent volume
- `kubectl describe pvc mypvc` - Describe persistent volume claim
- `kubectl create -f pv.yaml` - Create persistent volume
- `kubectl create -f pvc.yaml` - Create persistent volume claim
- `kubectl get pv -o wide` - List persistent volumes with details
- `kubectl get storageclass` - List storage classes
- `kubectl get sc` - Shorthand for getting storage classes
- `kubectl describe sc standard` - Describe storage class
- `kubectl create -f storageclass.yaml` - Create storage class
- `kubectl patch storageclass standard -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'` - Set default storage class

## RBAC and Security

### RBAC Management
- `kubectl create role pod-reader --verb=get,list,watch --resource=pods` - Create role
- `kubectl create role pod-reader --verb=get --resource=pods --resource-name=readablepod` - Create role for specific resource
- `kubectl create rolebinding bob-reader --role=pod-reader --user=bob` - Create role binding for user
- `kubectl create rolebinding bob-reader --role=pod-reader --serviceaccount=default:sa-name` - Role binding for service account
- `kubectl create clusterrole pod-reader --verb=get,list,watch --resource=pods` - Create cluster role
- `kubectl create clusterrolebinding bob-reader --clusterrole=pod-reader --user=bob` - Create cluster role binding
- `kubectl get roles` - List roles
- `kubectl get rolebindings` - List role bindings
- `kubectl get clusterroles` - List cluster roles
- `kubectl get clusterrolebindings` - List cluster role bindings
- `kubectl describe role pod-reader` - Describe role
- `kubectl describe rolebinding bob-reader` - Describe role binding
- `kubectl describe clusterrole pod-reader` - Describe cluster role
- `kubectl describe clusterrolebinding bob-reader` - Describe cluster role binding
- `kubectl auth can-i create deployments` - Check if can create deployments
- `kubectl auth can-i list pods --namespace dev` - Check permission in namespace
- `kubectl auth can-i list pods --as bob` - Check permissions for another user
- `kubectl auth can-i '*' '*'` - Check all permissions
- `kubectl auth reconcile -f rbac.yaml` - Reconcile RBAC resources

### Security Context
- `kubectl run secure-pod --image=nginx --security-context='{runAsUser: 1000}'` - Run pod with security context
- `kubectl create serviceaccount mysa` - Create service account
- `kubectl get serviceaccounts` - List service accounts
- `kubectl describe serviceaccount mysa` - Describe service account
- `kubectl patch deployment myapp -p '{"spec": {"template": {"spec": {"serviceAccountName": "mysa"}}}}'` - Set service account
- `kubectl create token mysa` - Create token for service account
- `kubectl certificate approve myuser` - Approve certificate signing request
- `kubectl certificate deny myuser` - Deny certificate signing request
- `kubectl get csr` - List certificate signing requests
- `kubectl describe csr myuser` - Describe certificate signing request

## Node Management

### Node Operations
- `kubectl get nodes` - List nodes
- `kubectl get nodes -o wide` - List nodes with extra details
- `kubectl describe node minikube` - Describe node
- `kubectl top node` - Show node resource usage
- `kubectl cordon minikube` - Mark node as unschedulable
- `kubectl uncordon minikube` - Mark node as schedulable
- `kubectl drain minikube` - Drain node in preparation for maintenance
- `kubectl drain minikube --ignore-daemonsets` - Drain ignoring daemonsets
- `kubectl drain minikube --delete-emptydir-data` - Drain and delete emptydirs
- `kubectl drain minikube --force` - Force drain
- `kubectl drain minikube --timeout=30s` - Drain with timeout
- `kubectl taint nodes minikube key=value:NoSchedule` - Add taint to node
- `kubectl taint nodes minikube key=value:NoSchedule-` - Remove taint from node
- `kubectl label node minikube disktype=ssd` - Label node
- `kubectl top node minikube` - Show node resource usage
- `kubectl get node minikube -o jsonpath='{.status.capacity}'` - Show node capacity
- `kubectl get node minikube -o jsonpath='{.status.allocatable}'` - Show node allocatable resources
- `kubectl get node minikube -o jsonpath='{.status.conditions[?(@.type=="Ready")].status}'` - Check if node is ready
- `kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="InternalIP")].address}'` - Get node IPs
- `kubectl get nodes --selector='!node-role.kubernetes.io/master'` - Get non-master nodes

## Advanced Cluster Management

### Maintenance and Upgrade
- `kubectl drain node-1 --ignore-daemonsets --delete-emptydir-data` - Prepare node for maintenance
- `kubectl uncordon node-1` - Mark node as schedulable after maintenance
- `kubectl drain node-1 --grace-period=900` - Drain with extended grace period
- `kubectl drain node-1 --force --grace-period=0` - Force drain immediately
- `kubectl drain node-1 --selector=app!=critical` - Drain specific pods
- `kubectl cordon --selector="node-role.kubernetes.io/infra"` - Cordon all infrastructure nodes

### Resource Quotas and Limits
- `kubectl create quota my-quota --hard=cpu=1,memory=1G,pods=2` - Create resource quota
- `kubectl create quota my-quota --hard=limits.cpu=2,limits.memory=2G` - Create quota on limits
- `kubectl create quota my-quota --hard=requests.cpu=1,requests.memory=1G` - Create quota on requests
- `kubectl create quota object-counts --hard=configmaps=10,persistentvolumeclaims=4,pods=20,replicationcontrollers=20,secrets=10,services=10` - Object count quota
- `kubectl get quota` - List resource quotas
- `kubectl describe quota my-quota` - Describe resource quota
- `kubectl create limitrange mem-limit-range --default-request=memory=256Mi --default-limit=memory=512Mi` - Create memory limit range
- `kubectl create limitrange cpu-limit-range --default-request=cpu=200m --default-limit=cpu=500m` - Create CPU limit range
- `kubectl get limitrange` - List limit ranges
- `kubectl describe limitrange mem-limit-range` - Describe limit range

### API and Extension Resources
- `kubectl api-resources` - List all resource types
- `kubectl api-resources --namespaced=true` - List namespaced resources
- `kubectl api-resources --namespaced=false` - List cluster-scoped resources
- `kubectl api-resources --api-group=apps` - List resources in specific API group
- `kubectl api-resources --verbs=list,get` - List resources supporting specific verbs
- `kubectl api-versions` - List API versions
- `kubectl explain pods` - Documentation for pods
- `kubectl explain pods.spec` - Documentation for pod spec
- `kubectl explain pods.spec.containers` - Documentation for containers
- `kubectl explain deployment.spec.strategy` - Documentation for deployment strategy
- `kubectl explain pod.spec.containers --recursive` - Get full resource documentation
- `kubectl get apiservices` - List API services
- `kubectl get crd` - List custom resource definitions
- `kubectl describe crd prometheuses.monitoring.coreos.com` - Describe CRD
- `kubectl api-resources --verbs=list --namespaced -o name | xargs -n 1 kubectl get --show-kind --ignore-not-found -n NAMESPACE` - List all resources in namespace

## Context and Configuration

### Kubeconfig Management
- `kubectl config view` - Show merged kubeconfig
- `kubectl config view --minify` - Show only current context info
- `kubectl config view --flatten` - Show flattened kubeconfig
- `kubectl config get-contexts` - List all contexts
- `kubectl config current-context` - Show current context
- `kubectl config use-context minikube` - Switch to context
- `kubectl config set-context --current --namespace=mynamespace` - Set default namespace for current context
- `kubectl config set-context mycluster-context --cluster=mycluster --user=cluster-admin` - Set cluster and user for context
- `kubectl config rename-context old-name new-name` - Rename context
- `kubectl config delete-context minikube` - Delete context
- `kubectl config set-cluster mycluster --server=https://myserver:8443` - Set cluster address
- `kubectl config set-cluster mycluster --certificate-authority=path/to/my/ca.crt` - Set cluster CA
- `kubectl config set-credentials cluster-admin --client-certificate=path/to/my/client.crt --client-key=path/to/my/client.key` - Set credentials
- `kubectl config set-credentials cluster-admin --token=bearer_token` - Set credentials with token
- `kubectl config unset contexts.minikube` - Remove context section
- `kubectl config unset users.cluster-admin` - Remove user section
- `kubectl config use-context $(kubectl config get-contexts -o name | fzf)` - Interactive context selection

### Kubernetes Environment Setup
- `kubectl version` - Show client and server version
- `kubectl version --client` - Show only client version
- `kubectl version --short` - Show short version info
- `kubectl cluster-info` - Display cluster info
- `kubectl cluster-info dump` - Dump cluster state
- `kubectl proxy` - Start proxy to Kubernetes API server
-

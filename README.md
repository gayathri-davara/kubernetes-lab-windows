
# Kubernetes Lab on Windows

A hands-on Kubernetes project demonstrating cluster creation, application deployment, scaling, and self-healing on Windows using Docker Desktop, kind, and kubectl.

## Overview

This project builds a local three-node Kubernetes cluster with one control-plane node and two worker nodes. An NGINX web application is deployed, scaled from three to six replicas, and tested for automatic recovery after a Pod is deleted.

## Technology Stack

- **Operating System:** Windows
- **Linux Environment:** WSL 2 with Ubuntu
- **Container Runtime:** Docker Desktop
- **Orchestration:** Kubernetes
- **Cluster Tool:** kind (Kubernetes IN Docker)
- **CLI:** kubectl
- **Web Server:** NGINX

## Architecture

| Component | Purpose |
|---|---|
| Control Plane | Manages cluster state and workload scheduling |
| Worker Node 1 | Runs application Pods |
| Worker Node 2 | Runs application Pods |
| NGINX Deployment | Maintains the desired number of replicas |
| ReplicaSet | Ensures the desired number of Pods exists |

## Project Structure

```text
kubernetes-lab-windows/
├── README.md
├── kind-config.yaml
└── screenshots/
    ├── nodes-ready.png
    ├── nginx-deployment.png
    └── self-healing.png
```

## 1. Verify the Tools

Check the installed tools using PowerShell:

```powershell
wsl --status
docker version
kubectl version --client
kind version
```

## 2. Create the Kubernetes Cluster

The `kind-config.yaml` file defines one control-plane node and two worker nodes.

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

Create the cluster:

```powershell
kind create cluster --config kind-config.yaml
```

Verify the cluster:

```powershell
kubectl cluster-info --context kind-kind
kubectl get nodes
docker ps
kubectl get pods -n kube-system -o wide
```

**Result:** All three Kubernetes nodes reached the `Ready` state, and the listed Kubernetes system Pods were running.

## 3. Deploy NGINX

Create a Deployment with three replicas:

```powershell
kubectl create deployment web --image=nginx --replicas=3
```

Inspect the Deployment and Pods:

```powershell
kubectl get deployments
kubectl get pods -o wide
kubectl describe pod <pod-name>
```

**Result:** Three NGINX Pods were created and reached the `Running` and Ready states.

## 4. Scale the Deployment

Scale the NGINX Deployment from three replicas to six:

```powershell
kubectl scale deployment web --replicas=6
```

Verify the Deployment and Pods:

```powershell
kubectl get deployments
kubectl get pods -o wide
kubectl get pods -w
```

**Result:** All six NGINX Pods reached the `Running` and Ready states.

## 5. Test Kubernetes Self-Healing

Delete one of the NGINX Pods using its actual name:

```powershell
kubectl delete pod <pod-name>
```

Observe the replacement:

```powershell
kubectl get pods -w
```

**Result:** Kubernetes automatically created a replacement Pod to restore the Deployment's desired replica count of six.

## 6. Screenshots

The following screenshots document the actual results of the lab.

### Kubernetes Cluster

![Three Kubernetes nodes in Ready state](screenshots/nodes-ready.png)

### NGINX Deployment

![NGINX Deployment with six replicas](screenshots/nginx-deployment.png)

### Kubernetes Self-Healing

![Six running Pods after replacement](screenshots/self-healing.png)

> Add the three screenshots to the `screenshots/` directory using the filenames shown above.

## Results Summary

| Experiment | Verified Result |
|---|---|
| Cluster creation | 3 nodes: 1 control plane and 2 workers |
| Node health | 3/3 nodes Ready |
| Initial deployment | 3 NGINX replicas |
| Scaling | 3 to 6 replicas |
| Self-healing | Deleted Pod automatically replaced |
| Final deployment status | 6/6 Ready, 6 up-to-date, 6 available |

## Key Concepts Learned

- Kubernetes cluster architecture
- Control-plane and worker nodes
- Deployments, ReplicaSets, and Pods
- Container orchestration using Docker Desktop
- Replica scaling
- Pod scheduling
- Self-healing and desired-state reconciliation

## Conclusion

This lab demonstrated the fundamental Kubernetes workflow on Windows using kind and Docker Desktop. The successful deployment, scaling, and Pod replacement experiments verified workload management and Kubernetes self-healing.

## Author

**Gayathri Davara**

GitHub: [@gayathri-davara](https://github.com/gayathri-davara)

## References

- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [kind Documentation](https://kind.sigs.k8s.io/)
- [Docker Documentation](https://docs.docker.com/)

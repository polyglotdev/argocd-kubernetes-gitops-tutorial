# Argocd Kubernetes Gitops Tutorial

This repo contains all the code needed to follow along with our **[YouTube Tutorial](https://youtu.be/yj4O0wwkMQI)** or **[Written Article](https://kubernetestraining.io/blog/argocd-kubernetes-learn-gitops-github-helm-kustomize)**.

## Prerequisites

To follow along with this tutorial, you'll need:

- kubectl installed and configured ([https://youtu.be/IBkU4dghY0Y](https://youtu.be/IBkU4dghY0Y))
- Helm installed: ([https://kubernetestraining.io/blog/installing-helm-on-mac-and-windows](https://kubernetestraining.io/blog/installing-helm-on-mac-and-windows))
- A GitHub account: ([https://github.com/](https://github.com/))

## Install ArgoCD on your Cluster

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
kubectl create namespace argocd
helm install argocd argo/argo-cd --namespace argocd
```

## Access ArgoCD UI

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

## Retrieve Credentials

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

## API Calls

Here are commands that you can use to add grades to the Grade Submission API. **Windows Users should use Git Bash**.

```bash
curl -X POST http://localhost:<port>/grades \
  -H "Content-Type: application/json" \
  -d '{"name": "Harry", "subject": "Defense Against Dark Arts", "score": 95}'

curl -X POST http://localhost:<port>/grades \
  -H "Content-Type: application/json" \
  -d '{"name": "Ron", "subject": "Charms", "score": 82}'

curl -X POST http://localhost:<port>/grades \
  -H "Content-Type: application/json" \
  -d '{"name": "Hermione", "subject": "Potions", "score": 98}'
```

To verify, you can get all grades with:

```bash
curl http://localhost:<port>/grades
```

## Kubernetes Training

If you found this guide helpful, check out our [Kubernetes Training course](https://kubernetestraining.io/)

```ascii
                        +---------------------+
                        |                     |
                        |  Kubernetes Cluster |
                        |                     |
                        +-----------+---------+
                                    |
                                    |
         +--------------------------|---------------------------+
         |                          |                           |
+--------v---------+     +----------v-----------+    +---------v--------+
|                  |     |                      |    |                  |
|    Node 1        |     |      Node 2          |    |     Node N       |
|                  |     |                      |    |                  |
| +--------------+ |     | +--------------+     |    | +--------------+ |
| |              | |     | |              |     |    | |              | |
| |   kubelet    | |     | |   kubelet    |     |    | |   kubelet    | |
| |              | |     | |              |     |    | |              | |
| +--------------+ |     | +--------------+     |    | +--------------+ |
| |              | |     | |              |     |    | |              | |
| |   kube-proxy | |     | |   kube-proxy |     |    | |   kube-proxy | |
| |              | |     | |              |     |    | |              | |
| +--------------+ |     | +--------------+     |    | +--------------+ |
|                  |     |                      |    |                  |
| +--------------+ |     | +--------------+     |    | +--------------+ |
| | Pod A        | |     | | Pod C        |     |    | | Pod X        | |
| | +----------+ | |     | | +----------+ |     |    | | +----------+ | |
| | |Container1| | |     | | |Container1| |     |    | | |Container1| | |
| | +----------+ | |     | | +----------+ |     |    | | +----------+ | |
| | +----------+ | |     | | +----------+ |     |    | | +----------+ | |
| | |Container2| | |     | | |Container2| |     |    | | |Container2| | |
| | +----------+ | |     | | +----------+ |     |    | | +----------+ | |
| +--------------+ |     | +--------------+     |    | +--------------+ |
|                  |     |                      |    |                  |
| +--------------+ |     | +--------------+     |    | +--------------+ |
| | Pod B        | |     | | Pod D        |     |    | | Pod Y        | |
| | +----------+ | |     | | +----------+ |     |    | | +----------+ | |
| | |Container1| | |     | | |Container1| |     |    | | |Container1| | |
| | +----------+ | |     | | +----------+ |     |    | | +----------+ | |
| +--------------+ |     | +--------------+     |    | +--------------+ |
+------------------+     +----------------------+    +------------------+

+-----------------+      +------------------+     +------------------+
| Control Plane   |      |                  |     |                  |
| +--------------+|      | etcd             |     | kube-scheduler   |
| | API Server   ||----->| (cluster state)  |     | (assigns pods    |
| +--------------+|      |                  |     |  to nodes)       |
+-----------------+      +------------------+     +------------------+
        ^
        |                +------------------+
        |                | controller-mgr   |
        +----------------|                  |
                         | (maintains       |
                         |  desired state)  |
                         +------------------+
```

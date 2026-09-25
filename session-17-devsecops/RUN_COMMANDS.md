# Demo Run Commands

These are the commands used to run and verify the demo with Docker and Kubernetes.

## Docker

```bash
# Check available images
docker image ls

# Start the existing image
docker run --rm -d --name session17-demo -p 5001:5001 hey-cicd:latest

# Verify the container
docker ps
curl http://localhost:5001/health
curl http://localhost:5001/api/status

# View container logs
docker logs session17-demo
```

Open the dashboard at <http://localhost:5001>.

## Kubernetes with Minikube

```bash
# Check the cluster
minikube status
kubectl get nodes -o wide

# Start Minikube if it is stopped
minikube start --driver=docker

# Deploy the existing manifests
kubectl apply -f demo/k8s/deployment.yaml -f demo/k8s/service.yaml
kubectl rollout status deployment/session17-python --timeout=120s

# Check the deployment and service
kubectl get deployment session17-python
kubectl get pods -l app=session17-python -o wide
kubectl get service session17-python
```

The manifest referenced a remote image. For the local Minikube run, the existing
image was loaded and the live deployment was pointed to it:

```bash
minikube image load hey-cicd:latest
kubectl set image deployment/session17-python session17-python=hey-cicd:latest
kubectl patch deployment session17-python --type='strategic' \
  -p '{"spec":{"template":{"spec":{"containers":[{"name":"session17-python","imagePullPolicy":"IfNotPresent"}]}}}}'

kubectl rollout status deployment/session17-python --timeout=120s
kubectl get pods -l app=session17-python -o wide
```

## Verify the Kubernetes app

```bash
MINIKUBE_IP=$(minikube ip)
curl "http://${MINIKUBE_IP}:30001/health"
curl "http://${MINIKUBE_IP}:30001/api/status"
```

Open the Kubernetes dashboard at <http://192.168.49.2:30001>.

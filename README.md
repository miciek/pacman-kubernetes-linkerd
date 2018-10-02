# Pac-Man multiplayer server - Service Meshes

This repository contains all deployment-related stuff regarding [pacman-multiplayer-fp](https://github.com/miciek/pacman-multiplayer-fp) project. 

To build fat-jar, docker image and deploy to `minikube` with `istio` sidecars, do:
```
sbt assembly
eval $(minikube docker-env)
docker build -t pacman-backend:v1 .
kubectl delete po -l app=backend
kubectl apply -f <(istioctl kube-inject --debug -f kube/pacman.yaml)
```

The application can be built and deployed to [Docker](https://www.docker.com/) and/or [Kubernetes](https://kubernetes.io) with [Istio](https://istio.io/docs/setup/kubernetes/quick-start.html) service mesh. Frontend application can be found in [Reactive Pac-Man repository](https://github.com/miciek/web-pacman-react-bacon).

The following assumes that you have `docker`, `minikube`, `kubectl` and `istioctl` commands available. Please refer to [Minikube section in this tutorial to set it up before moving on](https://istio.io/docs/setup/kubernetes/quick-start.html)

If deploying to `minikube`, please use `$GATEWAY_URL` instead of `localhost`. You can obtain this URL using `export GATEWAY_URL=$(minikube ip):$(kubectl get svc istio-ingress -n istio-system -o 'jsonpath={.spec.ports[0].nodePort}')`.


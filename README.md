# Pac-Man multiplayer server - Service Meshes
This repository contains all deployment-related stuff regarding [pacman-collectibles](https://github.com/miciek/pacman-collectibles) project. These two repositories are helper code repositories for my talk [Tearing down the pyramid... using functions and meshes](https://speakerdeck.com/miciek/tear-down-this-pyramid-dot-dot-dot-using-functions-and-meshes).

## Bootstrapping the cluster
In order to run examples from the talk you will need:
- [docker](https://www.docker.com/) installed,
- [minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) installed,
- `minikube ip` inserted in [linkerd.yaml](default/linkerd.yaml) file,
- output of `echo "$(minikube ip) pacman.prod"` inserted in `/etc/hosts` file,
- `kubectl apply -f default` to deploy services and deployments to `default` namespace,
- `kubectl apply -f pacman` to deploy services and deployments to `pacman` namespace,
- `eval $(minikube docker-env)` (so that local docker commands use docker inside `minikube`),
- go to [pacman.prod:4140](http://pacman.prod:4140) in your browser.

You can check and compare the state of your cluster with the desired one by doing:
```
> kubectl get po,svc -n default
NAME        READY     STATUS    RESTARTS   AGE
l5d-p52ln   2/2       Running   4          10h

NAME         TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)          AGE
kubernetes   ClusterIP      10.96.0.1        <none>           443/TCP          12h
l5d          LoadBalancer   10.104.134.178   192.168.99.100   4140:32544/TCP   12h

> kubectl get po,svc -n pacman
NAME                            READY     STATUS    RESTARTS   AGE
backend-54bfcbbbcb-mwlbk        1/1       Running   2          8h
backend-test-9db749db4-dbhb8    1/1       Running   1          5h
collectibles-6975f8ddf4-cm7jt   0/1       ErrImageNeverPull   0          7s
frontend-794d4cb455-67rjb       1/1       Running   2          8h
frontend-test-8fdf45fbf-9qj72   1/1       Running   1          5h

NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
backend         ClusterIP   10.107.193.160   <none>        8080/TCP   12h
backend-test    ClusterIP   10.106.151.104   <none>        8080/TCP   12h
collectibles    ClusterIP   10.97.67.129     <none>        8080/TCP   12h
frontend        ClusterIP   10.107.123.12    <none>        5000/TCP   12h
frontend-test   ClusterIP   10.97.194.192    <none>        5000/TCP   12h
```

All services besides one should be deployed correctly. In order to get `collectibles`, you need to build it yourself.

## Building & deploying missing pacman-collectibles service
Check out code inside [pacman-collectibles](https://github.com/miciek/pacman-collectibles) and run:
```
sbt assembly
eval $(minikube docker-env)
docker build -t pacman-collectibles:v1 .
kubectl apply -f pacman/pacman-collectibles.yaml
```
This will build fat-jar, docker image and deploy the service to your cluster.

## Other services
The application can be built and deployed to [Docker](https://www.docker.com/) and/or [Kubernetes](https://kubernetes.io) with [Linkerd 1](https://linkerd.io) service mesh. All apps besides `pacman-collectibles` are already build and pushed to global `docker.io` repository. You can find the code for those applications in the following repositories:

- `pacman-frontend` code can be found in [Reactive Pac-Man repository](https://github.com/miciek/web-pacman-react-bacon),
- `pacman-backend` code can be found in [Pac-Man multiplayer server repository](https://github.com/miciek/pacman-multiplayer-fp) (look at `add-collectibles-support` branch).

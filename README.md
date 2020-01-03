# Istio customizable installation for Minikube

This repository tries to provide a customizable installation and configure an Istio mesh for DEVELOPMENT use. With this configuration you will be able to run Istio on a 4MB cluster


Check the `values-minikube.yaml` file to see the values overrided from the default Istio installation

### Enabled services: 
- Grafana
- Kiali
- Jaeger 


# How to use
* `minikube start --memory 4096` If you have more memory I sugest increase the memory
* `kubectl create ns istio-system`
* `kubectl apply -f 1-istio-init.yaml`
* `kubectl apply -f 2-istio-minikube-reduced.yaml`
* `kubectl apply -f 3-kiali-secret.yaml`
* `kubectl apply -f 4-label-default-namespace.yaml`
* Wait until all pods are up and running, you can watch it with `kubectl get po -n istio-system -w`
* `minikube ip` 
* Visit $MINIKUBE_IP:31000 on the browser to access Kiali. credential admin - admin
* Visit $MINIKUBE_IP:31001 on the browser to access Jaeger
* Visit $MINIKUBE_IP:31002 on the browser to access Grafana

## Install Istio demo

```
export ISTIO_VERSION=1.3.0
curl -L https://git.io/getLatestIstio | sh -
cd istio-$ISTIO_VERSION 
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
export INGRESS_HOST=$(minikube ip)
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
export GATEWAY=$INGRESS_HOST:$INGRESS_PORT
echo $GATEWAY/productpage
```

Visit the address printed on the Browser


### Cleanup

* Stopping the application
``samples/bookinfo/platform/kube/cleanup.sh``

* Remove any kubectl port-forward processes that may be running
`killall kubectl`

## To generate the two yaml files with the last version of Istio

* Install helm https://helm.sh/docs/intro/install/
* Download and extract the Istio from https://github.com/istio/istio/releases/
* helm template ./kubernetes/helm/istio-init --name istio-init --namespace istio-system > istio-init.yaml
* helm template ./kubernetes/helm/istio --name istio --namespace istio-system --values values-minikube.yaml > istio-main-minikube-reduced.yaml

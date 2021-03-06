# ELK on AKS

## ECK

https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-quickstart.html

```bash
cd ECK
RG=ELK-RG
CLUSTER=ELK-AKS

az group create --name $RG -l southeastasia -o table
az aks create --resource-group $RG --name $CLUSTER --node-count 3 --generate-ssh-keys
az aks get-credentials --resource-group $RG --name $CLUSTER

kubectl apply -f eck-1.5.0-all-in-one.yaml
kubectl apply -f elasticsearch.yaml

kubectl logs -f quickstart-es-default-0 
kubectl get service quickstart-es-http

PASSWORD=$(kubectl get secret quickstart-es-elastic-user -o=jsonpath='{.data.elastic}' | base64 --decode)
ESIP=$(kubectl describe svc quickstart-es-http| grep IP: | awk '{print $2;}')

curl -u "elastic:$PASSWORD" -k "https://$ESIP:9200"



kubectl create namespace logging
kubectl apply -f fluent-bit-service-account.yaml
kubectl apply -f fluent-bit-role.yaml
kubectl apply -f fluent-bit-role-binding.yaml
kubectl apply -f fluent-bit-configmap.yaml

```

## Elastic OSS K8s yamls

```bash
cd self-hosted
kubectl create ns elastic-selfhosted
kubectl config set-context --current --namespace elastic-selfhosted
kubectl apply -f .
```

## Elastic OSS Helm Chart

https://github.com/elastic/helm-charts

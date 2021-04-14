# ELK on AKS

## ECK 
cd ECK
RG=ELK-RG
CLUSTER=ELK-AKS

az group create --name $RG -l southeastasia -o table
az aks create --resource-group $RG --name $CLUSTER --node-count 3 --generate-ssh-keys
az aks get-credentials --resource-group $RG --name $CLUSTER

kubectl apply -f https://download.elastic.co/downloads/eck/1.5.0/all-in-one.yaml
kubectl apply -f elasticsearch.yaml

kubectl logs -f quickstart-es-default-0 
kubectl get service quickstart-es-http

PASSWORD=$(kubectl get secret quickstart-es-elastic-user -o=jsonpath='{.data.elastic}' | base64 --decode)
ESIP=$(kubectl describe svc quickstart-es-http| grep IP: | awk '{print $2;}')

curl -u "elastic:$PASSWORD" -k "https://20.198.203.162:9200"



kubectl create namespace logging
wget https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/fluent-bit-service-account.yaml
wget https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/fluent-bit-role.yaml
wget https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/fluent-bit-role-binding.yaml

wget https://raw.githubusercontent.com/fluent/fluent-bit-kubernetes-logging/master/output/elasticsearch/fluent-bit-configmap.yaml
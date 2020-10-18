# kubernetes-intro

gcloud config list project

gcloud config set compute/zone us-central1-b

gcloud container clusters create io

gsutil cp -r gs://spls/gsp021/* .

kubectl create deployment nginx --image=nginx:1.10.0

kubectl get pods

kubectl expose deployment nginx --port 80 --type LoadBalancer

kubectl get services

curl http://<External IP>:80       ---- 34.69.158.244
  
  
cat pods/monolith.yaml

kubectl create -f pods/monolith.yaml
kubectl get pods
kubectl describe pods monolith

# Interacting with pods
By default, pods are allocated a private IP address and cannot be reached outside of the cluster. Use the kubectl port-forward command to map a local port to a port inside the monolith pod.
kubectl port-forward monolith 10080:80
curl http://127.0.0.1:10080

curl -u user http://127.0.0.1:10080/login
TOKEN=$(curl http://127.0.0.1:10080/login -u user|jq -r '.token')

curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure

kubectl logs monolith
kubectl logs -f monolith

kubectl exec monolith --stdin --tty -c monolith /bin/sh
ping -c 3 google.com



cat pods/secure-monolith.yaml

kubectl create secret generic tls-certs --from-file tls/
kubectl create configmap nginx-proxy-conf --from-file nginx/proxy.conf
kubectl create -f pods/secure-monolith.yaml

kubectl create -f services/monolith.yaml

gcloud compute firewall-rules create allow-monolith-nodeport \
  --allow=tcp:31000
  
gcloud compute instances list

kubectl get pods -l "app=monolith"
kubectl get pods -l "app=monolith,secure=enabled"

kubectl label pods secure-monolith 'secure=enabled'
kubectl get pods secure-monolith --show-labels

kubectl describe services monolith | grep Endpoints


cat deployments/auth.yaml
kubectl create -f deployments/auth.yaml
kubectl create -f services/auth.yaml
kubectl create -f deployments/hello.yaml
kubectl create -f services/hello.yaml

kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml


# Generate yaml file
 kubectl get svc nodehelloworld -o yaml

# Connect to the container
kubectl run -i --tty busybox --image=busybox --restart=Never -- sh

We can do a telnet <ip> <port> to verify connections betweens pods

# Expose a pod
kubectl expose pod nodehelloworld --type=LoadBalancer --name=node-helloworld-svc-lb

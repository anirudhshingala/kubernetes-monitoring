# Kops Kubernetes setup
1. Configure gcloud cli on you local machine and authenticate your account using gcloud init command
2. Once done Install kops to your local machine.
3. To create cluster using gcp compute machine and kops follow follwing steps
    a. Run this command to create bucet to store state of your cluster gsutil mb gs://kubernetes-clusters-anirudh07/
    a. kops create cluster learning.anirudhshingala.com --zones us-central1-a --state gs://kubernetes-clusters-anirudh07/ --project=gcp-learning-422517
4. Run this command to export kubeconfig file from remote state to our local machine
    kops export kubeconfig --admin --state gs://kubernetes-clusters-anirudh07/
5. To validate that you cluster is ready run 
    kops validate cluster learning.anirudhshingala.com --wait 10m --state gs://kubernetes-clusters-anirudh07/

    it it says auth required or something like this run
    ssh-keygen -t rsa
6. Once successful it will print message 
    Your cluster learning.anirudhshingala.com is ready

# Prometheus Grafana Setup
1. Add promethues helm chart to your localmachine
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
2. Download promethues helm chart into a folder
   cd to required path where you want your helm charts files to be present
   helm pull prometheus-community/prometheus --untar this will create a prometheus folder with all chart values
3. kubectl create ns monitoring to create a namespace to install prometheus and grafana
4. helm install prometheus ./prometheus/ -n monitoring to install prometheus helm chart

# Install nginx controller
1. kubectl create namespace ingress-nginx
2. helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
3. helm repo update
4. helm install nginx-ingress ingress-nginx/ingress-nginx --namespace ingress-nginx --set ingressClassResource.default=true
5. once this is done you can now creata ingress for your services
6. eg: kubectl create ingress prometheus --rule="prometheus.anirudhshingala.com/=prometheus-server:80" 

# Install grafana
1. helm repo add grafana https://grafana.github.io/helm-chart
2. helm repo update
3. helm pull grafana/grafana --untar
4. no need to create ingress manually, just enable ingress in values.yaml file and configure required values
5. helm install grafana grafana/ -n monitoring

### Create DNS records for grafana and prometheus using ip provided in command 
    kubectl get ingress 


# Install metrics server
1. kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
2. Once done confirm its working using below command
    kubectl top pods
    kubectl top nodes
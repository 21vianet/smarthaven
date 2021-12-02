# Grafana
We use the prometheus+grafana to monitor the k8s apps.  
* First we deploy the prometheus and grafana into kubernetes.
* Second we deploy the extra exporters to export the metrics.
* Third we make the custom grafana dashboard.

The details is [Here](grafana/README.md)
# Argocd
We use the Argocd to sync the deployment yaml into kubernetes.
* We commit deployment yamls into gitlab.
* Argocd sync the yamls automaticaly

The details is [Here](argocd/README.md)

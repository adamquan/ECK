# Elastic Cloud on Kubernetes
 Elastic Cloud on Kubernetes

1. Minikube

If you need to delete your old cluster, run:

```
minikube delete
```

Configure your new Minikube cluster:

```
minikube config set memory 12288
minikube config set cpus 4
minikube start
```

Launch the Kubernetes UI:

```
minikube dashboard
```

2. GKE

Download and install gcloud, kubectl on your laptop and run the following command using your GKE cluster info.

```
gcloud config set project elastic-sa
gcloud config set compute/zone us-central1-a
gcloud config set container/cluster aquan
gcloud auth login
gcloud container clusters get-credentials aquan --zone us-central1-a --project elastic-sa
kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=adam.quan@elastic.co
kubectl create clusterrolebinding adam.quan-cluster-admin-binding --clusterrole=cluster-admin --user=adam.quan@elastic.co
```

3. Deploy Elastic Cloud on Kubernetes (ECK)

```
kubectl apply -f https://download.elastic.co/downloads/eck/0.9.0/all-in-one.yaml
```

It should just take a few moment.

4. Deploy Elasticsearch, Kibana, APM Server

```
kubectl apply -f apm_es_kibana.yaml
```

It might take a minute. Check the state of the Elasticsearch, Kibana and APM server pods for all of them to get ready and green.

```
kubectl get elasticsearch,kibana,apmserver
```

5. Access Kibana

Port forward:

```
echo `kubectl get secret elasticsearch-sample-es-elastic-user -o=jsonpath='{.data.elastic}' | base64 --decode`

```

Get password of the elastic user, so that you can log into Kibana: http://localhost:5601

```
kubectl port-forward service/kibana-sample-kb-http 5601
```

6. Deploy APM instrumented application

Get APM token:

```
echo `kubectl get secret apm-server-sample-apm-token -o=jsonpath='{.data.secret-token}' | base64 --decode`
```

Get APM server URL:

```
kubectl get services | grep apm
```

Edit the environment settings of the app.

Deploy the application:

```
kubectl apply -f shark-node.yaml
```

Expose as a service:

```
kubectl expose deployment shark-demo-deployment -n shark
```

Port forward:

```
kubectl port-forward service/shark-demo-deployment 8080 -n shark
```

Access the application: http://localhost:8080

7. Scale & upgrade

8. Deploy Metricbeat


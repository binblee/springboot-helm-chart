# Spring Boot Application Helm Chart Example

Update 10/20/2022:
- springboot version: 2.7.4
- java version: 17
- helm template updated


Update 2021/9/6:
- springboot: 2.3.12
- helm template updated for Kubernetes 1.19 

## Build and push docker image

Rename ```image``` in demoweb/docker-compose.yml.

```yaml
build:
    context: .
image: binblee/springboot-helm-chart:jre-17
```

Build docker image use docker-compose command.

```
cd demoweb
docker compose build
docker push xxxx
```

Unit test:
```
cd demoweb
docker compose up
```

Visit http://localhost:8080/ you should get 'Hello World.'


If you prefer classic docker command line:

```
cd demoweb
docker build -t xxxx .
docker push xxxx
```



## Deploy using helm

Rename image ```image.repository``` and ```tag``` in demoweb/charts/values.yaml.

```yaml
image:
  repository: binblee/springboot-helm-chart
  tag: jre-17
```



You should have a running Kubernetes cluster and [helm installed](https://docs.helm.sh/using_helm/#installing-helm) before proceed.



Deploy application using helm.

```
cd demoweb/charts
helm install demo ./springboot-demoweb/ 
```

Access demo application following NOTES:
```
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=springboot-demoweb,app.kubernetes.io/instance=demo" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace default $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  echo "Visit http://127.0.0.1:8080 to use your application"
  kubectl --namespace default port-forward $POD_NAME 8080:$CONTAINER_PORT
```

Uninstall demo application in cluster:
```
helm uninstall demo
```


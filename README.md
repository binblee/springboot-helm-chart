# Spring Boot Application Helm Chart Example


Update 2021/9/6:
- springboot: 2.3.12
- helm template updated for Kubernetes 1.19 

## Build and push docker image

Rename ```image``` in demoweb/docker-compose.yml.

```yaml
build:
    context: .
image: binblee/springboot-helm-chart
```

Build docker image use docker-compose command.

```
cd demoweb
docker-compose build
docker push xxxx
```



If you prefer classic docker command line, run these:

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
  tag: openjdk-9-jre
```



You should have a running Kubernetes cluster and [helm installed](https://docs.helm.sh/using_helm/#installing-helm) before proceed.



Deploy application using helm.

```
cd demoweb/charts
helm install demo -n springboot-demoweb ./springboot-demoweb/ 
```



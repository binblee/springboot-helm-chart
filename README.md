# Spring Boot Application Helm Chart Example

## Build and push docker image

Rename image name in demoweb/docker-compose.yml.

```
cd demoweb
docker-compose build
docker push xxxx
```


## Deploy using helm

Rename image name in demoweb/charts/values.yaml.

```
cd demoweb/charts
helm install -n springboot-demo springboot-demoweb/
```
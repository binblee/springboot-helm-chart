# Spring Boot Application Helm Chart Example

## Build docker image (JVM)

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

## Build docker image (GraalVM)

Springboot 3.0 supports build native exectuable using GraalVM, we can leverage this feature to build a native executable docker image. 

```
cd demoweb
docker compose build web-graalvm
```

[docker-compose.yml](docker-compose.yml) has all the details: native docker image is built with another Dockerfile: [Dockerfile.graalvm](Dockerfile.graalvm).

Also to be noted that a native plugin is added to [pom.xml](pom.xml)
```
<plugin>
  <groupId>org.graalvm.buildtools</groupId>
  <artifactId>native-maven-plugin</artifactId>
</plugin>
```


## Deploy using helm

Rename image ```image.repository``` and ```tag``` in [demoweb/charts/values.yaml](demoweb/charts/values.yaml) with your image name and tag.

```yaml
image:
  repository: binblee/springboot-helm-chart
  tag: graalvm-22.3-java17
```




You should have a running Kubernetes cluster and [helm installed](https://docs.helm.sh/using_helm/#installing-helm) before proceed.



Deploy application using helm.

```
cd demoweb/charts
helm install demo ./springboot-demoweb/ 
```

Run port-forward and test it:
```
➜ kubectl port-forward service/demo-springboot-demoweb 8080:80
➜ curl http://localhost:8080
```
You should see "Hello World."

Uninstall demo application in cluster:
```
helm uninstall demo
```

## JVM image vs GraalVM image

We have two docker images now, one is build with JVM, the other one is build with graalvm native-image. Size of native exectuable image is about 1/3 of the classic JVM one. Quite impressive.

```
springboot-helm-chart:jre-17         294MB
springboot-helm-chart:graalvm-22.3   78.9MB
```

For more details, please refer to [https://docs.spring.io/spring-boot/docs/current/reference/html/native-image.html#native-image](https://docs.spring.io/spring-boot/docs/current/reference/html/native-image.html#native-image)
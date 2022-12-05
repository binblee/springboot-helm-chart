# Spring Boot Application Helm Chart Example

## Build docker image (JVM)

Rename ```image``` in [demoweb/docker-compose.yml](demoweb/docker-compose.yml).

```yaml
build:
    context: .
image: binblee/springboot-helm-chart:jre-17
```

Build docker image use docker-compose command.

```
➜ cd demoweb
➜ docker compose build
➜ docker push xxxx
```

Unit test:
```
➜ cd demoweb
➜ docker compose up -d web
```

Visit http://localhost:8080/ you should get 'Hello World.'

Shut it down:
```
➜ docker compose down
```


If you prefer to use docker to build image:

```
➜ cd demoweb
➜ docker build -t xxxx .
➜ docker push xxxx
```

## Build docker image (GraalVM)

Springboot 3.0 supports build native exectuable using GraalVM, we can leverage this feature to build a native executable docker image. 

```
➜ cd demoweb
➜ docker compose build web-graalvm
```

[demoweb/docker-compose.yml](demoweb/docker-compose.yml) will tell you that native docker image is built with another Dockerfile: [demoweb/Dockerfile.graalvm](demoweb/Dockerfile.graalvm).

Also to be noted that a native plugin is added to [demoweb/pom.xml](demoweb/pom.xml)
```
<plugin>
  <groupId>org.graalvm.buildtools</groupId>
  <artifactId>native-maven-plugin</artifactId>
</plugin>
```

You can up and run two images with one command:
```
➜ cd demoweb
➜ docker compose up -d
```

You should get two healthy containers:
```
NAME                    COMMAND                  SERVICE             STATUS              PORTS
demoweb-web-1           "sh -c 'java $JAVA_O…"   web                 running (healthy)   0.0.0.0:8080->8080/tcp
demoweb-web-graalvm-1   "/demoweb"               web-graalvm         running (healthy)   0.0.0.0:8888->8888/tcp
```

Test JVM version with [http://localhost:8080](http://localhost:8080), while GraalVM version with [http://localhost:8888](http://localhost:8888)


## Deploy using helm

Rename image ```image.repository``` and ```tag``` in [demoweb/charts/springboot-demoweb/values.yaml](demoweb/charts/springboot-demoweb/values.yaml) with your image name and tag.

```yaml
image:
  repository: binblee/springboot-helm-chart
  tag: graalvm-22.3-java17
```




You should have a running Kubernetes cluster and [helm installed](https://docs.helm.sh/using_helm/#installing-helm) before proceed.



Deploy application using helm.

```
➜ cd demoweb/charts
➜ helm install demo ./springboot-demoweb/ 
```

Run port-forward and test it:
```
➜ kubectl port-forward service/demo-springboot-demoweb 8080:80
➜ curl http://localhost:8080
```
You should see "Hello World."

Uninstall demo application in cluster:
```
➜ helm uninstall demo
```

## JVM image vs GraalVM image

We have two docker images now, one is build with JVM, the other one is build with graalvm native-image. Size of native exectuable image is about 1/3 of the classic JVM one. Quite impressive.

```
springboot-helm-chart:jre-17         294MB
springboot-helm-chart:graalvm-22.3   78.9MB
```

For more details, please refer to [https://docs.spring.io/spring-boot/docs/current/reference/html/native-image.html#native-image](https://docs.spring.io/spring-boot/docs/current/reference/html/native-image.html#native-image)
# syntax=docker/dockerfile:1

#
# Build image
#
FROM eclipse-temurin:19-jdk-jammy AS build

WORKDIR /app

COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN ./mvnw dependency:resolve

COPY src ./src

RUN ./mvnw clean package

#
# Image
#
FROM eclipse-temurin:19-jre
COPY --from=build /app/target/*.jar /app.jar

ENV JAVA_OPTS=""

EXPOSE ${SERVER_PORT}

HEALTHCHECK --interval=10s --timeout=3s \
CMD curl -v --fail http://localhost:${SERVER_PORT} || exit 1

ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -jar /app.jar" ]

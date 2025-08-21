# ---------- Build ----------
FROM maven:3.9-eclipse-temurin-21-alpine AS build
WORKDIR /app
COPY pom.xml .
RUN mvn -q -DskipTests dependency:go-offline
COPY src ./src
RUN mvn -q -DskipTests package

# ---------- Runtime ----------
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar

# Ajustes de memoria en contenedor
ENV JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75"

# Tu app escucha en 8080 (por tu application.properties)
EXPOSE 8080

# Healthcheck simple a la raíz (no necesita Actuator)
HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget -qO- http://127.0.0.1:8080/ || exit 1

ENTRYPOINT ["sh","-c","java $JAVA_OPTS -jar app.jar"]

HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget -qO- http://127.0.0.1:8080/actuator/health || exit 1
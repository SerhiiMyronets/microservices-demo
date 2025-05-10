# === Build stage ===
FROM --platform=$BUILDPLATFORM eclipse-temurin:21.0.5_11-jdk AS builder

WORKDIR /app

COPY ["build.gradle", "gradlew", "./"]
COPY gradle gradle
RUN chmod +x gradlew
RUN ./gradlew downloadRepos

COPY . .
RUN ./gradlew installDist

# === Runtime stage ===
FROM eclipse-temurin:21.0.5_11-jre-alpine

ADD https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/download/v1.33.0/opentelemetry-javaagent.jar /otel/opentelemetry-javaagent.jar

ENV JAVA_TOOL_OPTIONS="-javaagent:/otel/opentelemetry-javaagent.jar"
ENV OTEL_SERVICE_NAME=adservice
ENV OTEL_METRICS_EXPORTER=none

WORKDIR /app

COPY --from=builder /app .

EXPOSE 9555

ENTRYPOINT ["/app/build/install/hipstershop/bin/AdService"]

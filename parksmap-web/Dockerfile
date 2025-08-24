# Builder stage
FROM registry.access.redhat.com/ubi8/openjdk-11 AS builder
USER root
# Install Maven
RUN microdnf install -y maven && microdnf clean all
WORKDIR /opt/app
COPY . .
RUN mvn clean package

# Runtime stage
FROM registry.access.redhat.com/ubi8/openjdk-11
WORKDIR /opt/app
COPY --from=builder /opt/app/target/parksmap-web.jar /parksmap.jar
CMD ["java", "-jar", "/parksmap.jar"]
EXPOSE 8080

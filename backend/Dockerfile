# Single-stage image that runs the app with Maven

FROM maven:3.9-eclipse-temurin-21

WORKDIR /app

# Copy Maven config and sources
COPY pom.xml .
COPY src ./src

# Expose Spring Boot port
EXPOSE 8080

# Run the app via Maven (like you do locally with ./mvnw spring-boot:run)
CMD ["mvn", "spring-boot:run"]


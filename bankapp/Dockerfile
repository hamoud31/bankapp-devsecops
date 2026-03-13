# ============================
# Build stage
# ============================
FROM maven:3.8.5-openjdk-17 AS build
WORKDIR /build

# Copy only pom.xml first to leverage Docker caching
COPY pom.xml .
RUN mvn -q -e -DskipTests dependency:resolve

# Copy the rest of the source code
COPY src ./src
RUN mvn clean package -DskipTests

# ============================
# Run stage
# ============================
FROM openjdk:17-ea-17-jdk-slim

# Create a non-root user and group
RUN groupadd --system appgroup \
    && useradd --system --create-home --gid appgroup appuser

WORKDIR /app

# Copy the built jar and set ownership
COPY --from=build /build/target/*.jar app.jar
RUN chown -R appuser:appgroup /app

# Switch to non-root user
USER appuser

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]


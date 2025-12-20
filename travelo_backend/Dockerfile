# ---------- Build stage ----------
FROM maven:3.9.9-eclipse-temurin-23 AS build
WORKDIR /app

# Copy only pom first (better caching)
COPY pom.xml ./
RUN mvn -q -DskipTests dependency:go-offline

# Copy sources and build
COPY src ./src
RUN mvn -q -DskipTests clean package

# ---------- Runtime stage ----------
FROM eclipse-temurin:23-jre
WORKDIR /app

# Create a non-root user (security best practice)
RUN useradd -m appuser
USER appuser

# Copy the built jar from build stage
COPY --from=build /app/target/*.jar /app/app.jar

# Spring Boot default port (confirm later if you changed it)
EXPOSE 8080

# Optional: safer JVM defaults inside containers
ENV JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75"

ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar /app/app.jar"]

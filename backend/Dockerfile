# --- Build stage ---
FROM eclipse-temurin:21-jdk AS build
WORKDIR /workspace
COPY pom.xml .
COPY .mvn .mvn
COPY mvnw .
RUN chmod +x mvnw && ./mvnw -q dependency:go-offline || true
COPY src src
RUN ./mvnw -q clean package -DskipTests

# --- Runtime stage ---
FROM eclipse-temurin:21-jre
WORKDIR /app
RUN useradd -r -u 1001 patho
COPY --from=build /workspace/target/*.jar app.jar
USER patho
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]

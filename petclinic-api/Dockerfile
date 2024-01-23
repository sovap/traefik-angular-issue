FROM maven:3.9.6-eclipse-temurin-17 AS buildstage

WORKDIR /app

COPY . .

RUN mvn package

FROM eclipse-temurin:17-jdk AS runstage

COPY --from=buildstage /app/target/spring-petclinic-rest-3.0.2.jar .

EXPOSE 9966

RUN adduser --system --group spring

USER spring

ENTRYPOINT ["java", "-jar", "spring-petclinic-rest-3.0.2.jar"]

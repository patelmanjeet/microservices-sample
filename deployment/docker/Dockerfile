FROM openjdk:11
ARG APP_DIR=/app
ARG JAR_FILE=*.jar

RUN mkdir -p ${APP_DIR}
WORKDIR ${APP_DIR}

COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","app.jar"]
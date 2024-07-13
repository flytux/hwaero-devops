#FROM gradle:jdk21-jammy
#RUN  apt update && apt install vim -y

FROM maven
RUN  apt update && apt install vim -y
COPY pom.xml /tmp/pom.xml
RUN mvn -B -f /tmp/pom.xml -s /usr/share/maven/ref/settings-docker.xml dependency:resolve

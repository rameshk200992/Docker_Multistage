FROM ubuntu:24.04  AS stage
RUN apt-get update 
RUN apt-get install -y openjdk-17-jdk 
RUN apt-get install -y wget tar && \
    rm -rf /var/lib/apt/lists/*


# Install Maven 3.9.11
RUN wget https://archive.apache.org/dist/maven/maven-3/3.9.11/binaries/apache-maven-3.9.11-bin.tar.gz && \
    tar -xzf apache-maven-3.9.11-bin.tar.gz -C /opt && \
    ln -s /opt/apache-maven-3.9.11 /opt/maven && \
    rm apache-maven-3.9.11-bin.tar.gz

ENV MAVEN_HOME=/opt/maven
ENV PATH=$MAVEN_HOME/bin:$PATH

COPY . /home/app
WORKDIR /home/app

RUN mvn package
WORKDIR /home/app/jenkins/scripts

FROM eclipse-temurin:17-jre

COPY --from=stage /home/app/target/*.jar /app/simple-java-maven-app-1.0-SNAPSHOT.jar
WORKDIR /app
#CMD ["java", "-jar", "simple-java-maven-app-1.0-SNAPSHOT.jar"]

#CMD ["java", "-cp", "target/simple-java-maven-app-1.0-SNAPSHOT.jar", "com.example.App"]
FROM ubuntu:24.04  AS stage
RUN apt-get update 
RUN apt-get install -y openjdk-17-jdk wget tar && \
    rm -rf /var/lib/apt/lists/*


# Install Maven 3.9.11
RUN wget https://archive.apache.org/dist/maven/maven-3/3.9.11/binaries/apache-maven-3.9.11-bin.tar.gz && \
    tar -xzf apache-maven-3.9.11-bin.tar.gz -C /opt && \
    ln -s /opt/apache-maven-3.9.11 /opt/maven && \
    rm apache-maven-3.9.11-bin.tar.gz

ENV MAVEN_HOME=/opt/maven
ENV PATH=$MAVEN_HOME/bin:$PATH


ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
ENV CATALINA_HOME=/opt/tomcat
ENV CATALINA_BASE=/opt/tomcat
ENV PATH="$CATALINA_HOME/bin:$PATH"


COPY . /home/app
WORKDIR /home/app

RUN ls -lah /home/app/src/main/webapp/images/

RUN mkdir -p /tmp/test
# RUN cp /home/app/src/main/webapp/images/mithunlogo.jpg /tmp/test/

RUN rm -rf /home/app/target
RUN mvn clean package

RUN useradd -m -U -d /opt/tomcat -s /bin/false tomcat && \
    cd /tmp && \
    wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.112/bin/apache-tomcat-9.0.112.tar.gz && \
    mkdir -p /opt/tomcat && \
    tar -xzf apache-tomcat-9.0.112.tar.gz -C /opt/tomcat --strip-components=1 && \
    rm apache-tomcat-9.0.112.tar.gz && \
    chown -R tomcat:tomcat /opt/tomcat && \
    chmod +x /opt/tomcat/bin/*.sh


RUN cp /home/app/target/maven-web-application.war /opt/tomcat/webapps/

HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 CMD curl -f http://localhost:8080/maven-web-application/ || exit 1

#CMD ["/opt/tomcat/bin/catalina.sh", "run"]


FROM tomcat:9.0-jdk17-temurin-jammy 


COPY --from=stage /home/app/target/maven-web-application.war  /usr/local/tomcat/webapps/maven-web-application.war

HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 CMD curl -f http://localhost:8080/maven-web-application/ || exit 1

CMD ["catalina.sh", "run"]
### Set the base image to Fedora
FROM jboss/base-jdk:7

### File Author / Maintainer
MAINTAINER "Robert Greathouse" "cyotee@gmail.com"

### Set Environment
ENV JBOSS_PATH /opt/jboss
ENV JBOSS_HOME $JBOSS_PATH/jboss-eap-6.4
ENV JAVA_AGENT_HOME=/opt/appdynamics/java_server_agent
ENV JBOSS_MODULES_SYSTEM_PKGS="org.jboss.byteman,com.singularity,org.jboss.logmanager"
#ENV JAVA_OPTS="-Xms1303m -Xmx1303m -XX:MaxPermSize=256m -Djava.net.preferIPv4Stack=true -Djboss.modules.system.pkgs=$JBOSS_MODULES_SYSTEM_PKGS -Djava.awt.headless=true -Djboss.modules.policy-permissions=true -Djboss.bind.address=0.0.0.0 -Djboss.bind.address.management=0.0.0.0 -javaagent:${JAVA_AGENT_HOME}/javaagent.jar -Xbootclasspath/p:$(ls ${JBOSS_HOME}/modules/system/layers/base/org/jboss/logmanager/main/jboss-logmanager-*.jar)"


### Install EAP 6.4.0
COPY installs/jboss-eap-6.4.0.zip /tmp/jboss-eap-6.4.0.zip
COPY installs/AppServerAgent.zip /tmp/AppServerAgent.zip
RUN echo "Installing JBoss EAP 6.4" && \
    unzip /tmp/jboss-eap-6.4.0.zip -d $JBOSS_PATH

USER root

RUN echo "Creating AppDynamics Java Server Agent install path." && \
    mkdir -p $JAVA_AGENT_HOME && \
    chown -R jboss:jboss $JAVA_AGENT_HOME && \
    chmod -R ug+rw $JAVA_AGENT_HOME && \
    chmod -R g+rw $JAVA_AGENT_HOME

USER jboss
RUN echo "Install AppDynamics Java Server Agent." && \
    unzip /tmp/AppServerAgent.zip -d $JAVA_AGENT_HOME

RUN echo "Adding JBoss admin user." && \
    $JBOSS_HOME/bin/add-user.sh admin admin123! --silent

### Configure EAP
RUN echo "JAVA_OPTS=\"\$JAVA_OPTS -Djboss.bind.address=0.0.0.0 -Djboss.bind.address.management=0.0.0.0 -javaagent:${JAVA_AGENT_HOME}/javaagent.jar -Xbootclasspath/p:$(ls ${JBOSS_HOME}/modules/system/layers/base/org/jboss/logmanager/main/jboss-logmanager-*.jar)\"" >> $JBOSS_HOME/bin/standalone.conf

ENV APPD_LOGS="$JAVA_AGENT_HOME/ver4.1.7.1/logs/$APPDYNAMICS_AGENT_NODE_NAME"

### Open Ports
EXPOSE 8080 9990 9999

### Start EAP
ENTRYPOINT $JBOSS_HOME/bin/standalone.sh

#-c standalone-full-ha.xml

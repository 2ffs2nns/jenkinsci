FROM jenkinsci/jenkins
MAINTAINER Eric Hoffmann eric@tigera.io

USER root
RUN mkdir /var/log/jenkins
RUN chown -R jenkins:jenkins /var/log/jenkins
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt
RUN echo 2.0 > /usr/share/jenkins/ref/jenkins.install.UpgradeWizard.state

USER jenkins

ENV JAVA_OPTS="-Xmx4192m"
ENV JENKINS_OPTS="--logfile=/var/log/jenkins/jenkins.log"

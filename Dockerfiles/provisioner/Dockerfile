# Pull base image  
FROM ubuntu:16.04

MAINTAINER Huan "h.zhou@uva.nl"


# ARG RABBITMQ_HOST=127.0.0.1
# ARG MONOG_HOST=127.0.0.1
ARG RELESE_VERSION=3.0-pre-alpha
# ARG RELESE_VERSION
ARG CERTS=/etc/grid-security/certificates

WORKDIR /root/

#install openssh and wget
ENV DEBIAN_FRONTEND=noninteractive 
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends 
RUN apt-get upgrade -y 
RUN apt-get update -y  
RUN apt-get install -y openssh-server wget inetutils-ping software-properties-common curl

##install java
# RUN apt-get install -y openjdk-8-jdk
# RUN apt-get install -y python-software-properties debconf-utils apt-utils
# RUN add-apt-repository -y ppa:webupd8team/java
# RUN apt-get update
# RUN "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | debconf-set-selections
# RUN echo debconf shared/accepted-oracle-license-v1-1 select true | debconf-set-selections
# RUN echo debconf shared/accepted-oracle-license-v1-1 seen true | debconf-set-selections
# RUN apt-get install -y java-common oracle-java8-installer
RUN mkdir /opt/jdk
WORKDIR /opt/
RUN wget --header "Cookie: oraclelicense=accept-securebackup-cookie" https://github.com/QCAPI-DRIP/DRIP-integration/releases/download/0.2.1/jdk-8u151-linux-x64.tar.gz
RUN tar -zxf  jdk-8u151-linux-x64.tar.gz -C /opt/jdk
RUN update-alternatives --install /usr/bin/java java /opt/jdk/jdk1.8.0_151/bin/java 100
RUN update-alternatives --install /usr/bin/javac javac /opt/jdk/jdk1.8.0_151/bin/javac 100

#Install provisioner
WORKDIR /root/
RUN mkdir /root/drip-provisioner/
WORKDIR /root/drip-provisioner/
RUN wget https://raw.githubusercontent.com/EGI-FCTF/fedcloud-userinterface/master/fedcloud-ui.sh
RUN chmod +x fedcloud-ui.sh
RUN ./fedcloud-ui.sh
RUN wget https://github.com/QCAPI-DRIP/DRIP-integradation/releases/download/$RELESE_VERSION/drip-provisioner-1.0-SNAPSHOT-jar-with-dependencies.jar
RUN wget https://github.com/QCAPI-DRIP/DRIP-integration/releases/download/0.2.3/etc.tar.gz
RUN tar -xzvf etc.tar.gz
RUN echo trusted.certificates.folder=$CERTS >> provisioner.properties
RUN echo domain.info.path=/root/drip-provisioner/etc >> provisioner.properties
RUN cat provisioner.properties

ENTRYPOINT echo rabbitmq.host=$RABBITMQ_HOST >> /root/drip-provisioner/provisioner.properties && sleep 10 & java -jar /root/drip-provisioner/drip-provisioner-1.0-SNAPSHOT-jar-with-dependencies.jar /root/drip-provisioner/provisioner.properties

#Build: docker build -t drip-provisioner .
#Run: docker run -e RABBITMQ_HOST=172.17.0.2 drip-provisioner

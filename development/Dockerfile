FROM ubuntu:20.04

RUN mkdir /usr/java

RUN set -ex \
    && apt-get update \
    && apt-get install -y -qq --no-install-recommends \
      wget ca-certificates curl

ENV JAVA_11_HOME="/usr/lib/jvm/java-11-amazon-corretto"
ENV JDK_11_HOME="/usr/lib/jvm/java-11-amazon-corretto"
ENV JRE_11_HOME="/usr/lib/jvm/java-11-amazon-corretto"
ENV JAVA_8_HOME="/usr/lib/jvm/java-1.8.0-amazon-corretto"
ENV JDK_8_HOME="/usr/lib/jvm/java-1.8.0-amazon-corretto"
ENV JRE_8_HOME="/usr/lib/jvm/java-1.8.0-amazon-corretto/jre"
ARG MAVEN_HOME="/opt/maven"
ARG MAVEN_VERSION=3.6.3
ARG MAVEN_DOWNLOAD_SHA512="c35a1803a6e70a126e80b2b3ae33eed961f83ed74d18fcd16909b2d44d7dada3203f1ffe726c17ef8dcca2dcaa9fca676987befeadc9b9f759967a8cb77181c0"

ARG MAVEN_CONFIG_HOME="/root/.m2"

ENV JAVA_HOME="$JAVA_8_HOME" \
    JDK_HOME="$JDK_8_HOME" \
    JRE_HOME="$JRE_8_HOME"

RUN set -ex \
    && apt-get update \
    && apt-get install -y -qq  java-common \
    # Install Corretto 8
    && wget -qO /var/tmp/amazon-corretto-8.deb https://corretto.aws/downloads/resources/8.332.08.1/java-1.8.0-amazon-corretto-jdk_8.332.08-1_amd64.deb \
    && dpkg --install /var/tmp/amazon-corretto-8.deb \
    && rm /var/tmp/amazon-corretto-8.deb \
    # Install Corretto 11
    && wget -qO /var/tmp/amazon-corretto-11.deb https://corretto.aws/downloads/resources/11.0.15.9.1/java-11-amazon-corretto-jdk_11.0.15.9-1_amd64.deb \
    && dpkg --install /var/tmp/amazon-corretto-11.deb \
    && rm /var/tmp/amazon-corretto-11.deb \
    # Note: We will use update-alternatives to make sure JDK8 has higher priority for all the tools
    && for tool_path in $JAVA_HOME/bin/*; do \
      tool=`basename $tool_path`; \
      update-alternatives --install /usr/bin/$tool $tool $tool_path 10000; \
      update-alternatives --set $tool $tool_path; \
    done

RUN set -ex \
    # Install Maven
    && mkdir -p $MAVEN_HOME \
    && curl -LSso /var/tmp/apache-maven-$MAVEN_VERSION-bin.tar.gz https://apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz \
    && echo "$MAVEN_DOWNLOAD_SHA512 /var/tmp/apache-maven-$MAVEN_VERSION-bin.tar.gz" | sha512sum -c - \
    && tar xzf /var/tmp/apache-maven-$MAVEN_VERSION-bin.tar.gz -C $MAVEN_HOME --strip-components=1 \
    && rm /var/tmp/apache-maven-$MAVEN_VERSION-bin.tar.gz \
    && update-alternatives --install /usr/bin/mvn mvn /opt/maven/bin/mvn 10000 \
    && mkdir -p $MAVEN_CONFIG_HOME

RUN set -ex \
    # Install Tomcat
    && mkdir /usr/local/tomcat \
    && wget -qO /var/tmp/tomcat.tar.gz https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.81/bin/apache-tomcat-8.5.81.tar.gz \
    && tar xvf /var/tmp/tomcat.tar.gz --strip-components=1 -C /usr/local/tomcat \
    && rm /var/tmp/tomcat.tar.gz
# Download base image ubuntu 20.04
FROM ubuntu:20.04

#FROM openjdk:8-jdk-alpine

# Labeling the custom image
LABEL maintainer = "hariharanragothaman@gmail.com"
LABEL version = "1.0"
LABEL description = "Custom Docker image for development"

# Disable prompt during packages installation
ARG DEBIAN_FRONTEND=noninteractive

RUN echo "Installing build dependencies" && apt-get update && apt-get -y upgrade && apt-get install -y build-essential && \
    apt-get install -y software-properties-common && apt-get install -y byobu curl git htop man unzip vim wget unzip zip


# Install OpenJDK-8
RUN apt-get update && \
    apt-get install -y openjdk-8-jdk && \
    apt-get install -y ant && \
    apt-get clean;

# Fix certificate issues
RUN apt-get update && \
    apt-get install ca-certificates-java && \
    apt-get clean && \
    update-ca-certificates -f;

# Setup JAVA_HOME -- useful for docker commandline
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64/
RUN export JAVA_HOME

# Installing Groovy
CMD ["groovysh"]

ENV GROOVY_HOME /opt/groovy

RUN set -o errexit -o nounset \
    && echo "Adding groovy user and group" \
    && groupadd --system --gid 1000 groovy \
    && useradd --system --gid groovy --uid 1000 --shell /bin/bash --create-home groovy \
    && mkdir --parents /home/groovy/.groovy/grapes \
    && chown --recursive groovy:groovy /home/groovy \
    && chmod --recursive 1777 /home/groovy \
    \
    && echo "Symlinking root .groovy to groovy .groovy" \
    && ln --symbolic /home/groovy/.groovy /root/.groovy

VOLUME /home/groovy/.groovy/grapes

WORKDIR /home/groovy

RUN apt-get update \
    && echo "Installing build dependencies" \
    && apt-get install --yes --no-install-recommends \
        dirmngr \
        fontconfig \
        gnupg \
        unzip \
        wget \
    && rm --recursive --force /var/lib/apt/lists/*

ENV GROOVY_VERSION 3.0.7
RUN set -o errexit -o nounset \
    && echo "Downloading Groovy" \
    && wget --no-verbose --output-document=groovy.zip "https://archive.apache.org/dist/groovy/${GROOVY_VERSION}/distribution/apache-groovy-binary-${GROOVY_VERSION}.zip" \
    \
    && echo "Importing keys listed in http://www.apache.org/dist/groovy/KEYS from key server" \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --batch --no-tty --keyserver ha.pool.sks-keyservers.net --recv-keys \
        7FAA0F2206DE228F0DB01AD741321490758AAD6F \
        331224E1D7BE883D16E8A685825C06C827AF6B66 \
        34441E504A937F43EB0DAEF96A65176A0FB1CD0B \
        9A810E3B766E089FFB27C70F11B595CEDC4AEBB5 \
        81CABC23EECA0790E8989B361FF96E10F0E13706 \
    \
    && echo "Checking download signature" \
    && wget --no-verbose --output-document=groovy.zip.asc "https://archive.apache.org/dist/groovy/${GROOVY_VERSION}/distribution/apache-groovy-binary-${GROOVY_VERSION}.zip.asc" \
    && gpg --batch --no-tty --verify groovy.zip.asc groovy.zip \
    && rm --recursive --force "${GNUPGHOME}" \
    && rm groovy.zip.asc \
    \
    && echo "Installing Groovy" \
    && unzip groovy.zip \
    && rm groovy.zip \
    && mv "groovy-${GROOVY_VERSION}" "${GROOVY_HOME}/" \
    && ln --symbolic "${GROOVY_HOME}/bin/grape" /usr/bin/grape \
    && ln --symbolic "${GROOVY_HOME}/bin/groovy" /usr/bin/groovy \
    && ln --symbolic "${GROOVY_HOME}/bin/groovyc" /usr/bin/groovyc \
    && ln --symbolic "${GROOVY_HOME}/bin/groovyConsole" /usr/bin/groovyConsole \
    && ln --symbolic "${GROOVY_HOME}/bin/groovydoc" /usr/bin/groovydoc \
    && ln --symbolic "${GROOVY_HOME}/bin/groovysh" /usr/bin/groovysh \
    && ln --symbolic "${GROOVY_HOME}/bin/java2groovy" /usr/bin/java2groovy \
    \
    && echo "Editing startGroovy to include java.xml.bind module" \
    && sed --in-place 's|startGroovy ( ) {|startGroovy ( ) {\n    JAVA_OPTS="$JAVA_OPTS --add-modules=ALL-SYSTEM"|' "${GROOVY_HOME}/bin/startGroovy"

USER groovy

RUN set -o errexit -o nounset \
    && echo "Testing Groovy installation" \
    && groovy --version

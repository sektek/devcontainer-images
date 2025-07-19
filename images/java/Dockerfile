ARG JDK_VERSION=24
FROM eclipse-temurin:${JDK_VERSION} AS jdk-stage

FROM sektek/devcontainer-base:latest

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
    locales \
    ; rm -rf /var/lib/apt/lists/*

ARG JAVA_HOME=/opt/java/openjdk

ENV JAVA_HOME=${JAVA_HOME}
ENV PATH=$JAVA_HOME/bin:$PATH
ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'

COPY --from=jdk-stage ${JAVA_HOME} ${JAVA_HOME}

CMD ["jshell"]

FROM jenkins/inbound-agent:latest AS jnlp

FROM ubuntu:22.04 AS base

COPY --from=jnlp /usr/local/bin/jenkins-agent /usr/local/bin/jenkins-agent
COPY --from=jnlp /usr/share/jenkins/agent.jar /usr/share/jenkins/agent.jar

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    sshpass \
    software-properties-common \
    curl \
    gnupg2 \
    lsb-release \
    ca-certificates \
    git \
    sudo \
    iptables \
    ansible \
 && rm -rf /var/lib/apt/lists/* \
 && mkdir -p /etc/apt/keyrings \
 && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg \
 && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
    https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
    | tee /etc/apt/sources.list.d/docker.list > /dev/null \
 && apt-get update && apt-get install -y \
    docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin \
 && rm -rf /var/lib/apt/lists/*

WORKDIR /opt/playbooks
COPY ./ansible/ .

ARG NODE_VERSION
ARG JAVA_VERSION
ARG GRADLE_VERSION
ARG PYTHON_VERSION

RUN ansible-playbook playbook.yml \
    -e node_version=${NODE_VERSION} \
    -e java_version=${JAVA_VERSION} \
    -e gradle_version=${GRADLE_VERSION} \
    -e python_version=${PYTHON_VERSION}

WORKDIR /home/jenkins/agent

ENTRYPOINT ["/usr/local/bin/jenkins-agent"]
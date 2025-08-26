FROM jenkins/inbound-agent:latest AS jnlp
FROM docker:24.0-dind AS dind

FROM ubuntu:22.04 AS base

COPY --from=jnlp /usr/local/bin/jenkins-agent /usr/local/bin/jenkins-agent
COPY --from=jnlp /usr/share/jenkins/agent.jar /usr/share/jenkins/agent.jar

COPY --from=dind /usr/local/bin/docker /usr/local/bin/docker
COPY --from=dind /usr/local/bin/dockerd /usr/local/bin/dockerd
COPY --from=dind /usr/local/bin/containerd /usr/local/bin/containerd
COPY --from=dind /usr/local/bin/containerd-shim* /usr/local/bin/
COPY --from=dind /usr/local/bin/runc /usr/local/bin/runc
COPY --from=dind /usr/local/libexec/docker/cli-plugins /usr/local/libexec/docker/cli-plugins

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
    ansible && \
    rm -rf /var/lib/apt/lists/*

# --- playbook uruchamiasz jeszcze jako root ---
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

COPY --from=jnlp /etc/passwd /etc/passwd
COPY --from=jnlp /etc/group /etc/group

USER jenkins
WORKDIR /home/jenkins/agent

ENTRYPOINT ["/usr/local/bin/jenkins-agent"]
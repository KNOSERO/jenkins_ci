# Jenkins agent
FROM jenkins/inbound-agent:latest as jnlp

FROM ubuntu:22.04-minimal as base

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
    ansible && \
    apt-get clean

WORKDIR /opt/playbooks

COPY ../ansible/ .

RUN ansible-playbook playbook.yml

WORKDIR /home/jenkins/agent

ENTRYPOINT ["/usr/local/bin/jenkins-agent"]
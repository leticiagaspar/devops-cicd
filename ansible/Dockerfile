# Image that provides Ansible, used by the pipeline to run the deployment
FROM python:3.12-slim

# SSH client, needed by Ansible to connect to the server
RUN apt-get update \
    && apt-get install -y --no-install-recommends openssh-client \
    && rm -rf /var/lib/apt/lists/*

# Ansible (the full package includes the community.docker collection)
RUN pip install --no-cache-dir "ansible==11.*"

WORKDIR /ansible

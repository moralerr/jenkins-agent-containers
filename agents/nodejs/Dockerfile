# syntax=docker/dockerfile:1

FROM jenkins/inbound-agent:3283.v92c105e0f819-8-jdk21

USER root

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive \
    NODE_VERSION=18 \
    NPM_CONFIG_LOGLEVEL=info

# Install Node.js, npm, and Angular CLI
RUN curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - \
    && apt-get update \
    && apt-get install -y nodejs \
    && npm install -g @angular/cli \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Verify installations
RUN node --version \
    && npm --version \
    && ng version

# Switch back to the Jenkins user
USER jenkins

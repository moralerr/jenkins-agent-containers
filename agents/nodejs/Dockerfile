# syntax=docker/dockerfile:1

FROM jenkins/inbound-agent:3206.vb_15dcf73f6a_9-3

# Switch to root user for installation
USER root

# Install curl (if not already installed) and Node.js LTS
RUN apt-get update && \
    apt-get install -y curl && \
    curl -fsSL https://deb.nodesource.com/setup_lts.x | bash - && \
    apt-get install -y nodejs

# Install Angular CLI
RUN npm install -g @angular/cli

# Switch back to the Jenkins user
USER jenkins

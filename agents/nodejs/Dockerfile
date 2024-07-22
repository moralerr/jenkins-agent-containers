# syntax=docker/dockerfile:1

FROM jenkins/inbound-agent:3256.v88a_f6e922152-1

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

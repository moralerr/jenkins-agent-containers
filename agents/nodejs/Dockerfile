# syntax=docker/dockerfile:1

# Use the Jenkins inbound agent as the base image
FROM jenkins/inbound-agent:3273.v4cfe589b_fd83-1

# Switch to root user for installation
USER root

# Update package lists, install curl, and Node.js LTS
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    curl -fsSL https://deb.nodesource.com/setup_lts.x | bash - && \
    apt-get install -y --no-install-recommends nodejs && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install latest npm version globally
RUN npm install -g npm@latest

# Install Angular CLI globally using npm
RUN npm install -g @angular/cli

# Switch back to the Jenkins user
USER jenkins

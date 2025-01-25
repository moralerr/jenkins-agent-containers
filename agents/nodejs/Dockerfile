# syntax=docker/dockerfile:1

# Use the Jenkins inbound agent as the base image
FROM jenkins/inbound-agent:3273.v4cfe589b_fd83-1

USER root

RUN sed -i 's|http://deb.debian.org/debian|http://ftp.us.debian.org/debian|g' /etc/apt/sources.list

# Install required packages and Node.js
RUN apt-get update && \
    apt-get install -y curl && \
    curl -fsSL https://deb.nodesource.com/setup_18.x | bash - && \
    apt-get install -y nodejs && \
    npm install -g @angular/cli && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Verify installations
RUN node --version && npm --version && ng version

# Switch back to Jenkins user
USER jenkins

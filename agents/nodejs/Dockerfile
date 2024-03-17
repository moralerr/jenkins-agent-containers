FROM jenkins/inbound-agent:3206.vb_15dcf73f6a_9-3

USER root

# Install Node.js and npm
RUN apt-get update && \
    apt-get install -y curl && \
    curl -sL https://deb.nodesource.com/setup_14.x | bash - && \
    apt-get install -y nodejs

# Install Angular CLI
RUN npm install -g @angular/cli

USER jenkins

# Verify installation
RUN node --version && npm --version && ng --version

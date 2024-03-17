FROM jenkins/inbound-agent:3206.vb_15dcf73f6a_9-3

USER root

# Update package lists
RUN apt-get update && apt-get upgrade -y

# Install nodejs
RUN apt-get install -y nodejs

# Install Angular CLI
RUN npm install -g @angular/cli

USER jenkins

# Verify installation
RUN node --version && npm --version && ng --version

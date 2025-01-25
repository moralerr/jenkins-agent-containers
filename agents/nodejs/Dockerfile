FROM jenkins/inbound-agent:4.13.3-1

USER root

# Recreate sources.list for apt
RUN echo "deb http://deb.debian.org/debian bookworm main" > /etc/apt/sources.list && \
    echo "deb http://security.debian.org/debian-security bookworm-security main" >> /etc/apt/sources.list && \
    echo "deb http://deb.debian.org/debian bookworm-updates main" >> /etc/apt/sources.list

# Update to a faster mirror
RUN sed -i 's|http://deb.debian.org/debian|http://ftp.us.debian.org/debian|g' /etc/apt/sources.list

# Install Node.js and Angular CLI
RUN apt-get update && \
    apt-get install -y curl && \
    curl -fsSL https://deb.nodesource.com/setup_18.x | bash - && \
    apt-get install -y nodejs && \
    npm install -g @angular/cli && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

USER jenkins

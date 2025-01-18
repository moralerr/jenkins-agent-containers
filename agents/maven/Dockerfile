# syntax=docker/dockerfile:1
FROM jenkins/inbound-agent:3273.v4cfe589b_fd83-1

USER root

# Install Maven
RUN apt-get update && apt install -y --no-install-recommends maven

# Set MAVEN_HOME
ENV MAVEN_HOME /usr/share/maven

# Add Maven and MAVEN_HOME to PATH
ENV PATH="$PATH:$MAVEN_HOME/bin"

# Switch back to jenkins user
USER jenkins

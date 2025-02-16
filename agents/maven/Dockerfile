# syntax=docker/dockerfile:1

# Use the Jenkins inbound agent image as the base image
FROM jenkins/inbound-agent:3283.v92c105e0f819-8-jdk21

# Switch to root user to install Maven
USER root

# Update package list, install Maven, and clean up
RUN apt-get update && \
    apt-get install -y --no-install-recommends maven && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Set the MAVEN_HOME environment variable
ENV MAVEN_HOME /usr/share/maven

# Add Maven and MAVEN_HOME to the PATH environment variable
ENV PATH="$PATH:$MAVEN_HOME/bin"

# Switch back to jenkins user
USER jenkins

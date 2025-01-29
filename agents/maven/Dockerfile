# syntax=docker/dockerfile:1

# Use the Jenkins inbound agent image as the base image
FROM jenkins/inbound-agent:3273.v4cfe589b_fd83-1

# Switch to root user to install Maven
USER root

# Update package list and install Maven without recommended packages
RUN apt-get update && apt-get install -y --no-install-recommends maven

# Set the MAVEN_HOME environment variable
ENV MAVEN_HOME /usr/share/maven

# Add Maven and MAVEN_HOME to the PATH environment variable
ENV PATH="$PATH:$MAVEN_HOME/bin"

# Switch back to jenkins user
USER jenkins

FROM jenkins/inbound-agent:3206.vb_15dcf73f6a_9-3

# Install OpenJDK (required by Maven)
RUN apt-get update && apt-get install -y --no-install-recommends openjdk-11-jdk

# Install Maven
RUN apt-get update && apt-get install -y --no-install-recommends maven

# Additional steps (optional)
#  * Set MAVEN_HOME (optional, but recommended for some tools)
#  * Copy your pom.xml and project files
#  * Set working directory

# CMD (optional)
#  * Specify the command to run Maven (e.g., mvn clean install)

# Agent Build Containers ![Build and Push Docker Images](https://github.com/moralerr/jenkins-agent-containers/actions/workflows/build-and-push-docker-images.yml/badge.svg)

Based off https://github.com/jenkinsci/docker-agent/blob/master/debian/Dockerfile

## Overview
This project provides Docker containers for Jenkins agents. These containers are pre-configured with the necessary tools and dependencies to run Jenkins jobs efficiently.

## Features
- Pre-installed Jenkins agent
- Based on Debian for stability and security
- Includes common build tools and dependencies

## Prerequisites
- Docker installed on your machine
- Basic knowledge of Docker and Jenkins

## Usage
To use these containers with your Jenkins setup, you can pull the images from the Docker registry and configure your Jenkins master to use them as agent templates.

## Building the Docker Image
To build the Docker image locally, run the following command:
```sh
docker build -t jenkins-agent:latest .
```

## Running the Docker Container
To run the Docker container, use the following command:
```sh
docker run -d jenkins-agent:latest
```

## Troubleshooting
### Common Issues
- **Docker Daemon Not Running**: Ensure that the Docker daemon is running on your machine.
- **Permission Denied**: Run Docker commands with `sudo` or add your user to the Docker group.

## Contributing
Contributions are welcome! Please fork the repository and submit pull requests for any enhancements or bug fixes.

## License
This project is licensed under the MIT License. See the LICENSE file for details.

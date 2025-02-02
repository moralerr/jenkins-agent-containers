FROM jenkins/inbound-agent:latest-jdk17

USER root

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    ansible \
    git \
    jq \
    lsb-release \
    curl \
    gnupg \
    unzip \
    python3-pip \
    software-properties-common && \
    rm -rf /var/lib/apt/lists/*


# Install Terraform using HashiCorp's APT repository
RUN curl -fsSL https://apt.releases.hashicorp.com/gpg | apt-key add - && \
    apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main" && \
    apt-get update && \
    apt-get install -y terraform && \
    rm -rf /var/lib/apt/lists/*

# Install Terragrunt (set the version as needed)
ENV TERRAGRUNT_VERSION=0.48.7
RUN curl -fsSL -o /tmp/terragrunt \
    https://github.com/gruntwork-io/terragrunt/releases/download/v${TERRAGRUNT_VERSION}/terragrunt_linux_amd64 && \
    chmod +x /tmp/terragrunt && \
    mv /tmp/terragrunt /usr/local/bin/terragrunt

USER jenkins
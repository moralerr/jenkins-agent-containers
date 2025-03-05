FROM jenkins/inbound-agent:3283.v92c105e0f819-8-jdk21

USER root

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    git \
    jq \
    lsb-release \
    curl \
    gnupg \
    unzip \
    python3 \
    python3-pip \
    software-properties-common && \
    rm -rf /var/lib/apt/lists/*

# Install Ansible via pip for the latest version
RUN pip3 install --no-cache-dir ansible --break-system-packages

# Install prerequisites and download Terraform manually for ARM64
RUN apt-get update && \
    apt-get install -y lsb-release apt-transport-https ca-certificates curl gnupg unzip && \
    rm -rf /var/lib/apt/lists/* && \
    \
    # Set the Terraform version (update as needed)
    TERRAFORM_VERSION=1.3.7 && \
    echo "Downloading Terraform $TERRAFORM_VERSION for arm64..." && \
    curl -fsSL https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_arm64.zip -o terraform.zip && \
    unzip terraform.zip && \
    mv terraform /usr/local/bin/ && \
    rm terraform.zip

# Install Terragrunt (set the version as needed)
ENV TERRAGRUNT_VERSION=0.48.7
RUN curl -fsSL -o /tmp/terragrunt \
    https://github.com/gruntwork-io/terragrunt/releases/download/v${TERRAGRUNT_VERSION}/terragrunt_linux_arm64 && \
    chmod +x /tmp/terragrunt && \
    mv /tmp/terragrunt /usr/local/bin/terragrunt

USER jenkins
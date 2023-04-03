FROM ubuntu:18.04

ENV DEBIAN_FRONTEND=noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes


RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    gnupg \
    curl \
    jq \
    git \
    iputils-ping \
    libcurl4 \
    libicu60 \
    libunwind8 \
    netcat \
    libssl1.0 \
    vim \
    wget \
    libc6 \
    libgcc1 \
    libgssapi-krb5-2 \
    libssl1.1 \
    libstdc++6 \
    zlib1g \
    unzip \
    apt-transport-https \
    software-properties-common \
    openjdk-17-jdk \
  && rm -rf /var/lib/apt/lists/*

#install podman
RUN echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_18.04/ /" | tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
RUN curl -L "https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_18.04/Release.key" | apt-key add -
RUN apt-get update && apt-get install -y podman && rm -rf /var/lib/apt/lists/*


#install az cli
RUN curl -sL https://aka.ms/InstallAzureCLIDeb | bash

#install kubectl
RUN curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
RUN chmod +x ./kubectl
RUN mv ./kubectl /usr/local/bin

#install helm
RUN curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash

RUN TERRAFORM_VERSION=$(curl -s https://checkpoint-api.hashicorp.com/v1/check/terraform | jq -r .current_version) \
    && curl -LO https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip \
    && unzip terraform_${TERRAFORM_VERSION}_linux_amd64.zip -d /usr/local/bin \
    && rm -f terraform_${TERRAFORM_VERSION}_linux_amd64.zip

#install kubelogin
RUN wget https://github.com/Azure/kubelogin/releases/download/v0.0.20/kubelogin-linux-amd64.zip
RUN unzip kubelogin-linux-amd64.zip
RUN chmod +x ./bin/linux_amd64/kubelogin && mv ./bin/linux_amd64/kubelogin /usr/local/bin

#install crane from https://github.com/google/go-containerregistry/tree/main/cmd/crane
RUN wget https://github.com/google/go-containerregistry/releases/download/v0.11.0/go-containerregistry_Linux_x86_64.tar.gz
RUN tar -xvf go-containerregistry_Linux_x86_64.tar.gz && mv crane /usr/local/bin


# Can be 'linux-x64', 'linux-arm64', 'linux-arm', 'rhel.6-x64'.
ENV TARGETARCH=linux-x64
WORKDIR /azp

COPY docker-credential-acr-env /usr/local/bin
COPY start.sh .
RUN chmod -R +x /usr/local/bin
RUN chmod +x start.sh


ENTRYPOINT [ "./start.sh" ]

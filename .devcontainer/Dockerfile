# Use Microsoft’s official Rocky Linux minimal devcontainer base
FROM almalinux:9.5

# Switch to root to install system packages
USER root

# Set the working directory in the container to /home/vscode
WORKDIR /home/vscode

# Install OS packages, Terraform, Python & pip, and Ansible Lint
RUN groupadd --gid 1000 vscode \
   && useradd  --uid 1000 --gid vscode --shell /bin/zsh --create-home vscode \
   && mkdir -p /home/vscode/.vscode-server/extensions \
   && chown -R vscode:vscode /home/vscode

   # 2) Install sudo and give wheel group passwordless sudo
RUN dnf -y install sudo \
    && echo "vscode ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers \
    && dnf -y update \
    && dnf -y install \
       epel-release

RUN dnf -y install \
       git \
       ansible \
       python3.12 \
       python3.12-pip \
       openssh-clients \
       yum-utils \
       wget \
       zsh \
    # Add the HashiCorp repo and install Terraform
    && curl -fsSL https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo \
    && yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo \
    && dnf -y install terraform \
    && curl -fsSL "https://dl.k8s.io/release/v1.33.0/bin/linux/amd64/kubectl" \
            -o /usr/local/bin/kubectl && \
            chmod +x /usr/local/bin/kubectl \
    # Install ansible-lint via pip
    && alternatives --install /usr/bin/python3 python3 /usr/bin/python3.9  1 \
    && alternatives --install /usr/bin/python3 python3 /usr/bin/python3.12  2 \
    && alternatives --install /usr/bin/pip3     pip3     /usr/bin/pip3.12   1 \
    && alternatives --set python3 /usr/bin/python3.12 \
    && alternatives --set pip3    /usr/bin/pip3.12 \
    && dnf clean all
# Back to non-root 'vscode' user
USER vscode

RUN sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

RUN pip3 install --user ansible-dev-tools \
    && kubectl completion zsh

# Set environment variables for non-root user
ENV SHELL /bin/zsh

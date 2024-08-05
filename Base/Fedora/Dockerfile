FROM fedora:40

ARG ARCH="amd64"
ARG USERNAME="devuser"
ARG GO_VERSION="1.22.5"
ARG NODE_VERSION="v20.16.0"
ARG TERRAFORM_VERSION="1.9.3"

# Install Base
RUN dnf install -y \
    git \
    curl \
    wget \
    zsh \
    tree \
    unzip \
    pkgconf \
    perl-FindBin \
    perl-IPC-Cmd \
    openssl-devel \
    && dnf install -y @development-tools \
    && dnf clean all \
    && rm -rf /var/cache/yum;

# Setup User
RUN useradd -ms /usr/bin/zsh ${USERNAME} \
    && usermod -aG wheel ${USERNAME}
RUN echo '%wheel ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
USER ${USERNAME}
WORKDIR /home/${USERNAME}
# Setup ZSH
RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true \
    && sed -i 's/robbyrussell/avit/' /home/${USERNAME}/.zshrc
# Set User ENV Vars
RUN mkdir -p /home/${USERNAME}/.local/bin \
    && echo "export PATH=/home/${USERNAME}/.local/bin:\$PATH" >> $HOME/.zshrc

# Install Terraform
RUN wget https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_${ARCH}.zip \
    && unzip terraform_${TERRAFORM_VERSION}_linux_${ARCH}.zip \
    && mv terraform /home/${USERNAME}/.local/bin \
    && rm -f terraform_${TERRAFORM_VERSION}_linux_${ARCH}.zip

# Install Go
RUN wget https://go.dev/dl/go${GO_VERSION}.linux-${ARCH}.tar.gz \
    && tar -zxf go${GO_VERSION}.linux-${ARCH}.tar.gz -C /home/${USERNAME}/.local \
    && rm -f go${GO_VERSION}.linux-${ARCH}.tar.gz \
    && echo "export GOROOT=/home/${USERNAME}/.local/go" >> /home/${USERNAME}/.zshrc \
    && echo "export PATH=\$GOROOT/bin:\$PATH" >> /home/${USERNAME}/.zshrc

# Install NodeJS
RUN wget https://nodejs.org/dist/${NODE_VERSION}/node-${NODE_VERSION}-linux-x64.tar.xz \
    && tar -xf node-${NODE_VERSION}-linux-x64.tar.xz -C /home/${USERNAME}/.local \
    && echo "export PATH=/home/${USERNAME}/.local/node-${NODE_VERSION}-linux-x64/bin:\$PATH" >> /home/${USERNAME}/.zshrc

# Install Rust
RUN curl https://sh.rustup.rs -sSf | bash -s -- -y \
    && echo "source /home/${USERNAME}/.cargo/env" >> /home/${USERNAME}/.zshrc

ENTRYPOINT [ "/usr/bin/zsh" ]
FROM ubuntu:24.04

ARG ARCH="amd64"
ARG USERNAME="devuser"
ARG GO_VERSION="1.22.5"
ARG NODE_VERSION="v20.16.0"
ARG TERRAFORM_VERSION="1.9.3"

# Setup base
RUN apt-get update \
    && apt-get install -y \
    git \
    curl \
    wget \
    zsh \
    tree \
    unzip \
    pkg-config \
    libssl-dev \
    build-essential \
    sudo \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Setup User
RUN useradd -ms /usr/bin/zsh ${USERNAME} \
    && usermod -aG sudo ${USERNAME}
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
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
    && rm -f terraform_${TERRAFORM_VERSION}_linux_${ARCH}.zip LICENSE.txt

# Install Go
RUN wget https://go.dev/dl/go${GO_VERSION}.linux-${ARCH}.tar.gz \
    && tar -zxf go${GO_VERSION}.linux-${ARCH}.tar.gz -C /home/${USERNAME}/.local \
    && rm -f go${GO_VERSION}.linux-${ARCH}.tar.gz \
    && echo "export GOROOT=/home/${USERNAME}/.local/go" >> /home/${USERNAME}/.zshrc \
    && echo "export PATH=\$GOROOT/bin:\$PATH" >> /home/${USERNAME}/.zshrc

# Install NodeJS
RUN wget https://nodejs.org/dist/${NODE_VERSION}/node-${NODE_VERSION}-linux-x64.tar.xz \
    && tar -xf node-${NODE_VERSION}-linux-x64.tar.xz -C /home/${USERNAME}/.local \
    && echo "export PATH=/home/${USERNAME}/.local/node-${NODE_VERSION}-linux-x64/bin:\$PATH" >> /home/${USERNAME}/.zshrc \
    && rm -rf node-${NODE_VERSION}-linux-x64.tar.xz

# Install Rust
RUN curl https://sh.rustup.rs -sSf | bash -s -- -y \
    && echo "source /home/${USERNAME}/.cargo/env" >> /home/${USERNAME}/.zshrc

# Install Lazygit
RUN echo $(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep -Po '"tag_name": "v\K[^"]*')
# RUN LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep -Po '"tag_name": "v\K[^"]*') \
RUN curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/latest/download/lazygit_0.43.1_Linux_x86_64.tar.gz" \
    && tar -zxf lazygit.tar.gz lazygit \
    && mv lazygit /home/${USERNAME}/.local/bin \
    && rm -f lazygit.tar.gz


ENTRYPOINT [ "/usr/bin/zsh" ]
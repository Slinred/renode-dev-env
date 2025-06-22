FROM debian:bookworm-slim

# Crete a non-root user
ARG USERNAME=devuser
ARG USER_UID=1000
ARG USER_GID=1000

RUN apt update \
    && apt install -y sudo \
    && groupadd --gid $USER_GID $USERNAME \
    && useradd --create-home --shell /bin/bash --uid $USER_UID --gid $USER_GID $USERNAME \
    && echo "$USERNAME ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    # Clean up apt cache
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install dependencies
# See https://renode.readthedocs.io/en/latest/advanced/building_from_sources.html#prerequisites
RUN apt-get update \
    && apt-get install -y build-essential git wget unzip curl \
    automake cmake autoconf libtool g++ coreutils policykit-1 libgtk-3-dev uml-utilities gtk-sharp3 python3 python3-pip \
    # In devcontainer we also need gtk2 for build to succeed
    gtk-sharp2 libgtk2.0-cil-dev libglib2.0-cil-dev \
    # Clean up apt cache
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Mono
RUN apt install dirmngr ca-certificates gnupg \
    && gpg --homedir /tmp --no-default-keyring --keyring /usr/share/keyrings/mono-official-archive-keyring.gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF \
    && echo "deb [signed-by=/usr/share/keyrings/mono-official-archive-keyring.gpg] https://download.mono-project.com/repo/debian stable-buster main" | tee /etc/apt/sources.list.d/mono-official-stable.list \
    && apt update \
    && apt install -y mono-complete \
    # Clean up apt cache
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install .NET 
RUN wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh \
    && chmod +x dotnet-install.sh \
    && ./dotnet-install.sh --channel 8.0 --install-dir /usr/share/dotnet \
    && ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet \
    && rm dotnet-install.sh

USER $USERNAME
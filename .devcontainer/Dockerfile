# Base image: ROS 2 Jazzy on Ubuntu 24.04
FROM althack/ros2:jazzy-full

# Prevent interactive prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Core Robotics Stack: MoveIt 2, Gazebo Harmonic, and hardware controllers
RUN apt-get update && apt-get install -y \
    sudo \
    curl \
    git \
    build-essential \
    ros-jazzy-moveit \
    ros-jazzy-ros-gz \
    ros-jazzy-ros2-control \
    ros-jazzy-ros2-controllers \
    python3-colcon-common-extensions \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

# NanoClaw Runtime: Node.js (v24 stable) and Node Version Manager (NVM)
ENV NVM_DIR /usr/local/nvm
RUN mkdir -p $NVM_DIR \
    && curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash \
    && . $NVM_DIR/nvm.sh \
    && nvm install 24 \
    && nvm use 24 \
    && nvm alias default 24 \
    && npm install -g npm@latest \
    && npm install --no-audit --no-fund --yes -g yarn
# Set path to the specific version installed by NVM
ENV PATH $NVM_DIR/versions/node/v24.14.1/bin:$PATH

# Non-root user configuration
ARG USERNAME=ros_user
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Force create/modify group and user
RUN groupadd --gid $USER_GID --force $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m --non-unique $USERNAME \
    # Grant passwordless sudo
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    # Hardware & Serial access
    && usermod -aG video,dialout $USERNAME

# Project workspace configuration
USER $USERNAME
WORKDIR /workspaces/ItemSorter

# Persistent environment sourcing for interactive shells
RUN echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc \
    && echo 'export NVM_DIR="/usr/local/nvm"' >> ~/.bashrc \    
    && echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ~/.bashrc
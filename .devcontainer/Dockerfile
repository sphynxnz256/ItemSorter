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
    ros-jazzy-ros-gz-sim \
    ros-jazzy-ros-gz-bridge \
    ros-jazzy-ros2-control \
    ros-jazzy-ros2-controllers \
    ros-jazzy-gz-ros2-control \
    python3-colcon-common-extensions \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

# Node.js Environment: NVM-managed v24 runtime with dynamic version linking
ENV NVM_DIR="/usr/local/nvm"
RUN mkdir -p $NVM_DIR \
    && curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash \
    && . $NVM_DIR/nvm.sh \
    && nvm install 24 \
    && nvm alias default 24 \
    && nvm use default \
    && ln -s /usr/local/nvm/versions/node/$(nvm version default) /usr/local/nvm/current
# Use the 'current' shortcut to find Node.js binaries.
ENV PATH="$NVM_DIR/current/bin:$PATH"

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
RUN echo 'source /opt/ros/jazzy/setup.bash' >> /home/ros_user/.bashrc \
    && echo 'export NVM_DIR="/usr/local/nvm"' >> /home/ros_user/.bashrc \
    && echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> /home/ros_user/.bashrc
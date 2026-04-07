# Base image: ROS 2 Jazzy on Ubuntu 24.04
FROM althack/ros2:jazzy-full

# Prevent interactive prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Core Robotics Stack: MoveIt 2, Gazebo Harmonic, and hardware controllers
RUN apt-get update && apt-get install -y \
    sudo \
    ros-jazzy-moveit \
    ros-jazzy-ros-gz \
    ros-jazzy-ros2-control \
    ros-jazzy-ros2-controllers \
    python3-colcon-common-extensions \
    && rm -rf /var/lib/apt/lists/*

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
RUN echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc
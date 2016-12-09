FROM ros:kinetic

# install build tools
RUN apt-get update && apt-get install -y \
      ninja-build \
      python-catkin-tools \
      python-rosdep \
      python-wstool

# setup catkin workspace
ENV CATKIN_WS=/root/catkin_ws
RUN mkdir -p $CATKIN_WS/src
WORKDIR $CATKIN_WS

# clone project
RUN wstool init src && \
    wstool merge -t src \
      https://raw.githubusercontent.com/googlecartographer/cartographer_ros/master/cartographer_ros.rosinstall && \
    wstool update -t src

RUN rosdep update && \
    rosdep install -y \
      --from-paths src \
      --ignore-src \
      --rosdistro ${ROS_DISTRO} \
      --as-root=apt:false && \
    rm -rf /var/lib/apt/lists/*

# build ros package
WORKDIR $CATKIN_WS
ENV TERM xterm
ENV PYTHONIOENCODING UTF-8
RUN catkin config \
      --extend /opt/ros/$ROS_DISTRO \
      --install && \
    catkin build

RUN apt update && apt install -y \
      byobu \
      ros-{$ROS_DISTRO}-robot && \
    rm -rf /var/lib/apt/lists/*

# nvidia-docker hooks
LABEL com.nvidia.volumes.needed="nvidia_driver"
ENV PATH /usr/local/nvidia/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/nvidia/lib:/usr/local/nvidia/lib64:${LD_LIBRARY_PATH}

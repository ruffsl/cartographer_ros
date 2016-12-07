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

# HACK, replacing shell with bash for later source, catkin build commands
RUN mv /bin/sh /bin/sh-old && \
    ln -s /bin/bash /bin/sh

# build ros package
WORKDIR $CATKIN_WS
ENV TERM xterm
ENV PYTHONIOENCODING UTF-8
RUN catkin config \
      --extend /opt/ros/$ROS_DISTRO \
      --install && \
    catkin build

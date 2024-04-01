# we build this docker based on a docker with ros already installed
FROM ros:noetic-perception-focal

LABEL maintainer="shuoyang@andrew.cmu.edu"
ENV REFRESH_AT 2022-03-22

# nvidia-container-runtime
ENV NVIDIA_VISIBLE_DEVICES \
    ${NVIDIA_VISIBLE_DEVICES:-all}
ENV NVIDIA_DRIVER_CAPABILITIES \
    ${NVIDIA_DRIVER_CAPABILITIES:+$NVIDIA_DRIVER_CAPABILITIES,}graphics

# install necessary dependencies 
RUN   if [ "x$(nproc)" = "x1" ] ; then export USE_PROC=1 ; \
      else export USE_PROC=$(($(nproc)/2)) ; fi && \
      apt-get update && apt-get install -y \
      vim \
      libatlas-base-dev \
      libeigen3-dev \
      libgoogle-glog-dev \
      libsuitesparse-dev \
      python3-catkin-tools python3-osrf-pycommon \
      python3-matplotlib \
      gfortran \
      autoconf \
      coinor-libipopt-dev \
      libgfortran5 \
      curl \
      libopenmpi-dev \
      apt-utils \
      software-properties-common \
      build-essential \
      libssl-dev \
      wget \
      libtool \
      cmake

# install python 3.8 
RUN apt-get update && \
    apt-get install -y python3.8
RUN update-alternatives --install /usr/bin/python python /usr/bin/python3.8 1
RUN curl https://bootstrap.pypa.io/pip/get-pip.py -o get-pip.py
RUN python get-pip.py
RUN rm get-pip.py
RUN pip --version

# change timezone (this is very important otherwise many ROS topic time will be strange)
ENV TZ=America/New_York
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# lcm dependencies, notice ContactImplicitMPC.jl needs 2.68.3, we try 2.64.6 first
RUN apt-cache madison libglib2.0-dev
RUN apt-get install -y libglib2.0-0 libglib2.0-dev
# WORKDIR /tmp
# RUN wget http://archive.ubuntu.com/ubuntu/pool/main/g/glib2.0/libglib2.0-0_2.68.4-1ubuntu1_amd64.deb
# RUN dpkg -i libglib2.0-0_2.68.4-1ubuntu1_amd64.deb
 

# make unitree workspace
ENV SUPPORT_WS=/root/support_files
ENV UNITREE_WS=/root/unitree_ws
RUN mkdir -p $SUPPORT_WS 
RUN mkdir -p $UNITREE_WS/src
WORKDIR $UNITREE_WS
RUN /bin/bash -c "source /opt/ros/${ROS_DISTRO}/setup.bash; catkin init;"

   

# install necessary dependencies 
RUN   if [ "x$(nproc)" = "x1" ] ; then export USE_PROC=1 ; \
      else export USE_PROC=$(($(nproc)/2)) ; fi && \
      apt-get update && apt-get install -y \
      ros-${ROS_DISTRO}-ros-control \
      ros-${ROS_DISTRO}-gazebo-ros \
      ros-${ROS_DISTRO}-joy \
      ros-${ROS_DISTRO}-ros-controllers \
      ros-${ROS_DISTRO}-robot-state-publisher \
      ros-${ROS_DISTRO}-plotjuggler-ros



RUN echo "export ROS_MASTER_URI=http://localhost:11311;export ROS_IP=localhost;export ROS_HOSTNAME=localhost" >> ~/.bashrc

# To use rosparam load yaml files
RUN pip install pyyaml


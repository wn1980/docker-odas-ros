#FROM ros:kinetic-ros-core-xenial
#FROM ros:melodic-ros-core-bionic
FROM ros:noetic-ros-core-focal

LABEL maintainer="Waipot Ngamsaad <waipotn@hotmail.com>"

SHELL ["/bin/bash", "-c"]

ENV DEBIAN_FRONTEND noninteractive

RUN apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654

RUN sed -i -e 's/http:\/\/archive/mirror:\/\/mirrors/' -e 's/http:\/\/security/mirror:\/\/mirrors/' -e 's/\/ubuntu\//\/mirrors.txt/' /etc/apt/sources.list

RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y \
    apt-transport-https \
    build-essential \
    curl \
    git \
    wget \
    nano && \
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list' && \
    curl -L http://packages.osrfoundation.org/gazebo.key | apt-key add -

RUN apt-get update && apt-get upgrade -y 
RUN apt-get install -y \
    #ros-${ROS_DISTRO}-desktop-full \
    #ros-${ROS_DISTRO}-linux-peripheral-interfaces \
    #ros-${ROS_DISTRO}-diagnostics \
    #ros-${ROS_DISTRO}-navigation \
    #ros-${ROS_DISTRO}-hector-slam \
    #ros-${ROS_DISTRO}-octomap-server \
    #ros-${ROS_DISTRO}-octomap-rviz-plugins \
    ros-${ROS_DISTRO}-tf-conversions \
    python3-rosdep && \
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# install nodejs
RUN sh -c 'echo "deb https://deb.nodesource.com/node_10.x `lsb_release -cs` main" > /etc/apt/sources.list.d/nodesource.list' && \
    curl -sSL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | apt-key add -

RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y \
    nodejs \
    libfftw3-dev \
    libconfig-dev \
    libasound2-dev \
    libgbm-dev \
    libnss3-dev \
    python-dev \
    libssl-dev \
    libffi-dev \
    libatlas3-base \
    portaudio19-dev \
    ffmpeg \
    alsa-utils \
    gfortran \
    texinfo \
    python3-pip && \
    #pip install --upgrade pip \
    #pip install --upgrade setuptools \
    #pip install -U --no-cache-dir supervisor supervisor_twiddler && \
    pip install libconf numpy scipy \
    apt-get autoremove -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN rosdep init

# init rosdep
RUN rosdep fix-permissions && rosdep update

RUN mkdir -p /catkin_ws/src 

RUN cd /catkin_ws/src && \
    git clone https://github.com/introlab/audio_utils.git && \
    cd audio_utils && \
    git submodule update --init --recursive

RUN cd /catkin_ws/src && \
    git clone https://github.com/introlab/odas_ros.git && \
    cd odas_ros && \
    git submodule update --init --recursive

#RUN source /opt/ros/$ROS_DISTRO/setup.bash && \
#	cd /catkin_ws && \
#	catkin_make install -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/opt/ros/$ROS_DISTRO -DCATKIN_ENABLE_TESTING=0 && \
#	cd / && rm -rf catkin_ws

RUN source /opt/ros/$ROS_DISTRO/setup.bash && \
	cd /catkin_ws && \
	catkin_make

COPY ./odas.cfg /
#EXPOSE 11311 8558 9901

WORKDIR /

#CMD ["sudo", "-E", "/usr/local/bin/supervisord", "-c", "/app/supervisord.conf"]
CMD source /catkin_ws/devel/setup.bash && roslaunch odas_ros odas.launch configuration_path:=/odas.cfg

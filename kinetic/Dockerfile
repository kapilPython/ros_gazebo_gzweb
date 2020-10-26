FROM ubuntu:xenial

# setup environment
EXPOSE 8080
EXPOSE 7681
EXPOSE 11345

# install packages
RUN apt-get update && apt-get install -q -y --no-install-recommends \
    dirmngr \
    gnupg2 \
    lsb-release \
    build-essential \
    python-rosdep \
    python-rosinstall \
    python-vcstools \
    iputils-ping \
    curl \
    git \
    && rm -rf /var/lib/apt/lists/*

# setup keys
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654

# setup sources.list
RUN echo "deb http://packages.ros.org/ros/ubuntu xenial main" > /etc/apt/sources.list.d/ros1-latest.list

# setup environment
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8

RUN adduser --disabled-password --gecos '' gazebo-docker && \
    #mkdir -p /usr/src/gazebo-docker /catkin_ws/src && \
    #adduser -h /usr/src/gazebo-docker -D -H gazebo-docker -u 1000 && \
    #chown -R gazebo-docker:root /catkin_ws && chmod -R g+rwX /catkin_ws && \ 
    chown -R gazebo-docker:root /home/gazebo-docker && chmod -R g+rwX /home/gazebo-docker

# Set work directory
WORKDIR /home/gazebo-docker

ENV ROS_DISTRO kinetic

# bootstrap rosdep
#RUN rosdep init && \
#    rosdep update --rosdistro $ROS_DISTRO

# install ros packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    ros-kinetic-desktop=1.3.2-0* \
    && rm -rf /var/lib/apt/lists/*

# setup keys
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys D2486D2DD83DB69272AFE98867170598AF249743

# setup sources.list
RUN . /etc/os-release \
    && echo "deb http://packages.osrfoundation.org/gazebo/$ID-stable `lsb_release -sc` main" > /etc/apt/sources.list.d/gazebo-latest.list

# label gazebo packages
LABEL org.osrfoundation.gazebo7.sha256=b1aee9ca46363b8b8ae56503af3876466ced9d86b166b69af633204e74317024

# install gazebo packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    gazebo7=7.16.1-1* \
    libgazebo7-dev=7.16.1-1* \
    && rm -rf /var/lib/apt/lists/*

# install packages
RUN apt-get update && apt-get install -q -y --no-install-recommends \
    build-essential \
    cmake \
    imagemagick \
    libboost-all-dev \
    libgts-dev \
    libjansson-dev \
    libtinyxml-dev \
    mercurial \
    nodejs \
    nodejs-legacy \
    npm \
    pkg-config \
    psmisc \
    xvfb \
    && rm -rf /var/lib/apt/lists/*

# clone gzweb
RUN mkdir -p gzweb
ENV GZWEB_WS /home/gazebo-docker/gzweb
RUN git clone https://github.com/osrf/gzweb.git $GZWEB_WS
WORKDIR $GZWEB_WS

RUN apt-get update
RUN apt-get install curl
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt-get install -y nodejs

# build gzweb
RUN git checkout gzweb_1.4.0 \
    && xvfb-run -s "-screen 0 1280x1024x24" ./deploy.sh -m

USER gazebo-docker
# run gzserver and gzweb
CMD gzserver --verbose & npm start
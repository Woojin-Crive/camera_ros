ARG ROS_DISTRO=jazzy

FROM ros:${ROS_DISTRO}

RUN apt update && apt install -y \
    python3-pip \
    git \
    libyaml-dev \
    python3-yaml \
    python3-ply \
    python3-jinja2 \
    ninja-build \
    pkg-config \
    g++ \
    clang \
    libgnutls28-dev \
    libssl-dev \
    openssl \
    meson \
    python3-argcomplete \
    python3-colcon-common-extensions \
    libboost-system-dev \
    build-essential \
    libboost-dev \
    libtiff-dev \
    pybind11-dev \
    qtbase5-dev \
    libqt5core5a \
    libqt5widgets5 \
    cmake \
    libglib2.0-dev \
    libgstreamer-plugins-base1.0-dev

WORKDIR /root/libcamera
RUN git clone https://github.com/raspberrypi/libcamera.git . && \
    git checkout d83ff0a4ae4503bc56b7ed48cd142c3dd423ad3b && \
    meson setup build --buildtype=release -Dpipelines=rpi/vc4,rpi/pisp -Dipas=rpi/vc4,rpi/pisp -Dv4l2=true -Dgstreamer=enabled -Dtest=false -Dlc-compliance=disabled -Dcam=disabled -Dqcam=disabled -Ddocumentation=disabled -Dpycamera=enabled && \
    ninja -C build install && \
    ldconfig

RUN apt install -y \
    libudev-dev \
    tmux \
    iproute2 \
    wget \
    nano

WORKDIR /root/colcon_ws/src

RUN git clone https://github.com/christianrauch/camera_ros.git

WORKDIR /root/colcon_ws
RUN rosdep update && \
    rosdep install --from-paths ./src -i -y -r --rosdistro ${ROS_DISTRO} && \
    /bin/bash -c "source /opt/ros/${ROS_DISTRO}/setup.bash && colcon build --symlink-install" && \
    echo 'source /opt/ros/${ROS_DISTRO}/setup.bash' >> /root/.bashrc && \
    echo 'source /root/colcon_ws/install/setup.bash' >> /root/.bashrc && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /root

RUN echo "alias cm='colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release'" >> ~/.bashrc

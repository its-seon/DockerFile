FROM osrf/ros:humble-desktop-full

# nvidia-docker-runtime
ENV NVIDIA_VISIBLE_DEVICES ${NVIDIA_VISIBLE_DEVICES:-all}
ENV NVIDIA_DRIVER_CAPABILITIES ${NVIDIA_DRIVER_CAPABILITIES:+$NVIDIA_DRIVER_CAPABILITIES,}graphics

# Update and install packages
RUN apt-get update -y && \
    apt-get install -y nano wget git cmake build-essential

# Set the working directory
WORKDIR /root/home

# Clone PX4-Autopilot and setup
RUN git clone https://github.com/PX4/PX4-Autopilot.git --recursive && \
    bash PX4-Autopilot/Tools/setup/ubuntu.sh && \
    cd PX4-Autopilot && \
    make px4_sitl

# Return to /root/home for next operations
WORKDIR /root/home

# Clone Micro-XRCE-DDS-Agent and build
RUN git clone https://github.com/eProsima/Micro-XRCE-DDS-Agent.git && \
    cd Micro-XRCE-DDS-Agent && \
    mkdir build && \
    cd build && \
    cmake .. && \
    make && \
    make install && \
    ldconfig /usr/local/lib/

# Setup PX4 workspace and clone necessary repositories
WORKDIR /root/home/px4_ws/src
RUN git clone https://github.com/PX4/px4_msgs.git && \
    git clone https://github.com/PX4/px4_ros_com.git

WORKDIR /root/home/px4_ws
RUN /bin/bash -c "source /opt/ros/humble/setup.bash && \
    export CMAKE_PREFIX_PATH=/opt/ros/humble:$CMAKE_PREFIX_PATH && \
    colcon build"

CMD ["/bin/bash"]

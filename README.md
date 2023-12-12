# new board setup
Setup instructions for creating a fresh dev environment for the Jetson Orin Nano

Flash 128GB SD card with Jetson Orin Nano 20.04 image: https://developer.nvidia.com/downloads/embedded/l4t/r35_release_v4.1/jp512-orin-nano-sd-card-image.zip # JP 5.1.2 for now, switch to 6.0 & update this guide when StereoLabs updates ZED SDK for JP 6.0 Ubuntu 22.04

#installation:

#remove python 3.9, as it breaks some ROS2 humble packages

sudo apt purge -y python3.9 libpython3.9*

#Acquire and run ZED SDK install script: https://download.stereolabs.com/zedsdk/4.0/l4t35.4/jetsons

#Install pip3 version of pybind

python3 -m pip install --upgrade pybind11

#get ros_base humble source

sudo pip3 install rosinstall_generator

mkdir -p ~/ros2_humble/src

cd ~/ros2_humble/

rosinstall_generator ros_base --rosdistro humble --deps --tar > humble_ros2_base.repos

vcs import src < humble_ros2_base.repos

#install rosdep dependencies

sudo rosdep init

rosdep update

rosdep install --from-paths src --ignore-src --rosdistro humble -y

#build ros

colcon build --packages-up-to ros_base --symlink-install

#Source ros

source ~/ros2_humble/install/setup.bash

#install apt dependencies

sudo apt install python3-grpcio python3-websockets

#overwrite default python3-protobuf

python3 -m pip install --upgrade protobuf

#install pip dependencies

python3 -m pip install pyserial Adafruit-Blinka adafruit-circuitpython-ads1x15 smbus2 

#install sailbot packages

git clone git@github.com:wpisailbot/sailbot23-24.git

cd sailbot23-24

git submodule init

git submodule update

colcon build


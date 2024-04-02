# new board setup
Setup instructions for creating a fresh dev environment for the Jetson Orin Nano

Add storage to Jetson (SD or SSD, at least 128GB), flash Jetpack 5.1.2 or 5.1.3 using NVIDIA SDK manager (5.1.2 or 5.1.3 for now, switch to 6.0 & update this guide when StereoLabs updates ZED SDK for JP 6.0 Ubuntu 22.04)

# installation:

# Acquire and run ZED SDK install script: https://download.stereolabs.com/zedsdk/4.0/l4t35.4/jetsons

#setup

sudo apt update && sudo apt upgrade -y<br>
sudo adduser sailbot dialout<br>
sudo adduser sailbot tty<br>
sudo apt purge -y python3.9 libpython3.9*<br>
sudo apt install python3-pip<br>
python3 -m pip install --upgrade pip<br>
python3 -m pip install --upgrade pybind11<br>
python3 -m pip install onnx<br>
sudo apt install python-is-python3<br>

#ROS

locale  # check for UTF-8

sudo apt update && sudo apt install locales<br>
sudo locale-gen en_US en_US.UTF-8<br>
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8<br>
export LANG=en_US.UTF-8<br>

locale  # verify settings

sudo apt install software-properties-common<br>
sudo add-apt-repository universe<br>

sudo apt update && sudo apt install curl -y<br>
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg<br>

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null

sudo apt update && sudo apt install -y \
  python3-flake8-docstrings \
  python3-pip \
  python3-pytest-cov \
  ros-dev-tools
  
python3 -m pip install -U \
   flake8-blind-except \
   flake8-builtins \
   flake8-class-newline \
   flake8-comprehensions \
   flake8-deprecated \
   flake8-import-order \
   flake8-quotes \
   "pytest>=5.3" \
   pytest-repeat \
   pytest-rerunfailures
   
mkdir -p ~/ros2_humble/src<br>
cd ~/ros2_humble<br>
vcs import --input https://raw.githubusercontent.com/ros2/ros2/humble/ros2.repos src<br>

sudo apt upgrade

sudo rosdep init<br>
rosdep update<br>
rosdep install --from-paths src --ignore-src -y --skip-keys "fastcdr rti-connext-dds-6.0.1 urdfdom_headers"<br>

cd ~/ros2_humble/<br>
colcon build --symlink-install<br>

sudo apt autoremove --purge

echo 'source /home/sailbot/ros2_humble/install/setup.bash' >> ~/.bashrc<br>
source ~/.bashrc<br>

cd ~

#manually compile some dependencies

sudo apt install libgeographic-dev

mkdir ros2_ws<br>
cd ros2_ws<br>
mkdir src<br>
cd src<br>
git clone https://github.com/stereolabs/zed-ros2-interfaces.git<br>
git clone https://github.com/cra-ros-pkg/robot_localization.git<br>
git clone https://github.com/ros/diagnostics.git<br>
git clone https://github.com/ros-drivers/nmea_msgs.git<br>
git clone https://github.com/stereolabs/zed-ros2-wrapper.git<br>
git clone https://github.com/stereolabs/zed-ros2-interfaces.git<br>
git clone https://github.com/ros/angles.git<br>
git clone https://github.com/ros-geographic-info/geographic_info.git<br>
cd geographic_info/<br>
git checkout ros2<br>
cd ../..<br>
colcon build --symlink-install<br>

echo 'source /home/sailbot/ros2_ws/install/setup.bash' >> ~/.bashrc<br>
source ~/.bashrc<br>

cd ~


#Need repo rights for this (ssh key)
git clone git@github.com:wpisailbot/sailbot23-24.git<br>
cd  ~/sailbot23-24/sailbot_ws<br>
colcon build --symlink-install<br>
echo 'source /home/sailbot/sailbot23-24/sailbot_ws/install/setup.bash' >> ~/.bashrc<br>
source ~/.bashrc<br>

#install pip dependencies
python3 -m pip install pyserial numpy==1.21 adafruit-blinka opencv-python scikit-fuzzy protobuf grpcio pyproj geopy

#trim tab server
cd ~<br>
git clone git@github.com:wpisailbot/trim_tab_server.git<br>



# disable UART serial console, so we can talk to esp32

systemctl stop nvgetty<br>
systemctl disable nvgetty<br>
udevadm trigger<br>

sudo reboot


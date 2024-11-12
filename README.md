# new board setup
Setup instructions for creating a fresh dev environment for the Jetson Orin Nano

Add storage to Jetson (SD or SSD, at least 128GB), flash Jetpack 6.0 rev. 2 using NVIDIA SDK manager
# This MUST be done through SDK manager to ensure old bugged USB firmware is replaced. Flashing an SD card will not fix them.

# installation:

sudo apt update && sudo apt upgrade -y<br>
sudo apt install -y nvidia-jetpack <br> #Needed because something's wrong with sdk manager 6.0 rev.2 TensorRT

# Acquire and run ZED SDK install script: [https://stereolabs.sfo2.cdn.digitaloceanspaces.com/zedsdk/4.1/ZED_SDK_Tegra_L4T36.3_v4.1.3.zstd.run](https://stereolabs.sfo2.cdn.digitaloceanspaces.com/zedsdk/4.1/ZED_SDK_Tegra_L4T36.3_v4.1.3.zstd.run)

#setup

sudo adduser sailbot dialout<br>
sudo adduser sailbot tty<br>

#ROS

# Install ROS desktop and dev tools from deb packages
https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debs.html

echo 'source /opt/ros/humble/setup.bash' >> ~/.bashrc<br>
source ~/.bashrc<br>

cd ~

#manually compile some dependencies

sudo apt install libgeographic-dev libboost-python-dev

mkdir ros2_ws<br>
cd ros2_ws<br>
mkdir src<br>
cd src<br>
git clone https://github.com/stereolabs/zed-ros2-wrapper.git --recurse-submodules<br>
cd ..<br>
sudo rosdep init<br>
rosdep update<br>
rosdep install --from-paths src --ignore-src -y<br>
colcon build --symlink-install<br>

echo 'source /home/sailbot/ros2_ws/install/local_setup.bash' >> ~/.bashrc<br>
source ~/.bashrc<br>

cd ~

sudo apt install libcgal-dev

#Need repo rights for this (ssh key)<br>
git clone git@github.com:wpisailbot/sailbot24-25.git --recurse-submodules<br>
cd  ~/sailbot24-25/sailbot_ws<br>
colcon build --symlink-install<br>
echo 'source /home/sailbot/sailbot24-25/sailbot_ws/install/local_setup.bash' >> ~/.bashrc<br>
source ~/.bashrc<br>
cd ..<br>
sudo ./setup.sh<br>

#install pip dependencies<br>
python3 -m pip install --upgrade pyserial numpy==1.24.4 adafruit-blinka opencv-python scikit-fuzzy protobuf grpcio pyproj geopy esptool filterpy

#trim tab server<br>
sudo apt install -y python3-venv # fixes error about Platformio not being able to find Python
cd ~<br>
git clone git@github.com:wpisailbot/trim_tab_server.git<br>



# Instructions for enabling 4G modem:

sudo mmcli -m 0<br>
sudo nmcli c add type gsm ifname \<primary port from previous command output\> con-name cell apn Mint<br>
sudo nmcli r wwan on<br>
sudo reboot


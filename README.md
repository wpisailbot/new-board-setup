# new board setup
Setup instructions for creating a fresh dev environment for the Jetson nano

Expect this process to take at least one day, probably more.

1. Flash 128GB SD card with jetson nano 20.04 image: https://github.com/Qengineering/Jetson-Nano-Ubuntu-20-image

2. Update Jetson nano l4t release to 32.7 https://docs.nvidia.com/jetson/archives/r34.1/DeveloperGuide/text/SD/SoftwarePackagesAndTheUpdateMechanism.html#updating-a-jetson-device

3. Ensure you have 4G swap space (check with free -h), if not:  https://www.forecr.io/blogs/programming/how-to-increase-swap-space-on-jetson-modules

4. Build and install CMake from source, whatever the most recent version is, I used 3.27.8 https://github.com/Kitware/CMake. 3.26.X may work better, as there are some warnings generated building ROS in 3.27.X. Requires further testing.

5. Install pyenv, install & activate updated Python (I used 3.11.6). Ensure that running "python3" in a fresh shell launches python 3.11, not python 3.8.

6. Uninstall pybind 11 from apt: "sudo apt remove pybind11-dev", install from pip: "python3 -m pip install pybind11". This fixes an undocumented build error in rclpy on 20.04.

7. Install Python packages- normally, these come from ROS apt dependencies, but apt will get 3.8 versions, so we need to use pip as well- "python3 -m pip install empy lark catkin_pkg flake8-docstrings pytest-cov"

8. Build and install ROS2 Humble from source https://docs.ros.org/en/humble/Installation/Alternatives/Ubuntu-Development-Setup.html and add "source <ros_dir>/install/setup.sh" to ~/.bashrc  

9. Install Requests "python3 -m pip install requests"

10. Install ZED SDK for JetPack 4.6.X (L4T 32.7) https://www.stereolabs.com/developers/release/

11. Get the OMPL install script: https://ompl.kavrakilab.org/core/installation.html

INFO! The following steps are taken to correct a bug that currently exists in OMPL which prevents it from building on ARM. This may have been rectified since this was written. The fix is currently in this PR: https://github.com/ompl/ompl/pull/1100. if that has been merged, you may be able to build normally.

12. Clone ompl git into the same folder as the OMPL install script: git clone --recurse-submodules https://github.com/ompl/ompl.git" and cd into the folder.

13. Switch to the bugfix PR commit: "git checkout cef11bf6a587bcad3c338acb25d53a315b085d85"

14. Modify the OMPL install script by commenting out the line "git clone --recurse-submodules https://github.com/ompl/${OMPL}.git"

15. Run the script with -g and -p flags (use git source, and build python bindings): "chmod a+x install-ompl-ubuntu.sh && ./install-ompl-ubuntu.sh -g -p"

16. Clone the sailbot23-24 repo and follow its setup instructions: https://github.com/wpisailbot/sailbot23-24

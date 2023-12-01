# Robot Arm Setup on Raspberry Pi

Install Ubuntu 18.04.5 ARM 64 Server for Raspberry Pi and update the packages
```
sudo apt update
sudo apt upgrade
```
Install ROS Melodic
```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
sudo apt install curl 
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
sudo apt install ros-melodic-ros-base
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
```
Install the build tools and dependencies
```
sudo apt install -y build-essential sqlite3 ffmpeg python-pip
```
Install Raspberry PI dependencies
```
cd ~
git clone https://github.com/twming/robotic_arm_niryo
mv robotic_arm_niryo/catkin_ws .
cd catkin_ws
pip2 install -r src/requirements.txt
```
Install WiringPi and GPIO
```
cd ~/robotic_arm_niryo/dependencies/wiring_pi
chmod 777 build update
./build
```
Install ROS and dependencies
```
sudo apt install -y python-catkin-pkg python-pymodbus python-rosdistro python-rospkg python-rosdep-modules python-rosinstall python-rosinstall-generator python-wstool
sudo apt install -y ros-melodic-moveit ros-melodic-controller-manager ros-melodic-tf2-web-republisher ros-melodic-rosbridge-server ros-melodic-joint-state-publisher-gui ros-melodic-rosdoc-lite ros-melodic-roslint ros-melodic-joint-state-controller ros-melodic-joint-trajectory-controller ros-melodic-robot-state-publisher
```
Make change on HARDWARE_VERSION to one/ned/ned2 in CMakeLists.txt files on packages can_debug_tool,can_driver,common,conveyor_interface,dynamixel_sdk,end_effector_interface,joint_interface,niryo_robot_hardware_interface,tools_interface,ttl_driver
```
set(HARDWARE_VERSION "one" CACHE STRING "Robot model target")
```
Compile the package
```
cd ~/catkin_ws
catkin_make -j1 -l1
echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc
```
Other hardware setup
```
#--- config.txt
enable_uart=1
cmdline=nobtcmd.txt

include syscfg.txt
include usercfg.txt

#--- syscfg.txt
dtparam=i2c_arm=on
dtparam=spi=on

#--- usercfg.txt
enable_uart=1
init_uart_clock=16000000
dtoverlay=pi3-disable-bt
dtoverlay=uart0
dtparam=spi=on
dtparam=i2c1=on
dtparam=i2c0=on
dtparam=i2c_arm=on
dtparam=uart0=on
dtoverlay=mcp2515-can0-overlay,oscillator=16000000,interrupt=25

[pi4]
arm_64bit=1
dtoverlay=i2s-mmap
dtparam=i2s=on
dtoverlay=seeed-voicecard
```
Edit cmdline.txt, remove ttyAMA0 related 
```
#--- cmdline.txt and nobtcmd.txt
dwc_otg.lpm_enable=0 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline rootwait

# not like this:
dwc_otg.lpm_enable=0 console=ttyAMA0,115200 kgdboc=ttyAMA0,115200 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline rootwait
```
add to .bashrc
```
export ROOT_PATH=/home/niryo/catkin_ws
export LD_LIBRARY_PATH=$ROOT_PATH/devel/lib:/opt/ros/melodic/lib:$LD_LIBRARY_PATH
export ROS_ETC_DIR=/opt/ros/melodic/etc/ros
export CMAKE_PREFIX_PATH=$ROOT_PATH/devel:/opt/ros/melodic
export ROS_ROOT=/opt/ros/melodic/share/ros
export PYTHONPATH=$ROOT_PATH/devel/lib/python2.7/dist-packages:/opt/ros/melodic/lib/python2.7/dist-packages:/usr/lib/python2.7/dist-packages:$PYTHONPATH
export ROS_PACKAGE_PATH=$ROOT_PATH/devel/share:/opt/ros/melodic/share
export PATH=/opt/ros/melodic/bin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
export PKG_CONFIG_PATH=$ROOT_PATH/devel/lib/pkgconfig:/opt/ros/melodic/lib/pkgconfig
```
add TCP server ip address to your local ip address,e.g. 192.168.0.122
```
sudo ~/catkin_ws/src/niryo_robot_user_interface/config/default.yaml
```


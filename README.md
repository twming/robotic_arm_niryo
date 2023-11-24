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
mkdir -p catkin_ws/src
cd catkin_ws
git clone https://github.com/NiryoRobotics/ned_ros src
pip2 install -r src/requirements.txt
```
Install WiringPi and GPIO
```
cd ~/dependencies/wiringPi
chmod 777 build
./build
```
Install ROS and dependencies
```
sudo apt install -y python-catkin-pkg python-pymodbus python-rosdistro python-rospkg python-rosdep-modules python-rosinstall python-rosinstall-generator python-wstool
sudo apt install -y ros-melodic-moveit ros-melodic-controller-manager ros-melodic-tf2-web-republisher ros-melodic-rosbridge-server ros-melodic-joint-state-publisher-gui ros-melodic-rosdoc-lite ros-melodic-roslint ros-melodic-joint-state-controller ros-melodic-joint-trajectory-controller
```
Compile the package
```
cd ~/catkin_ws
catkin_make
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
 

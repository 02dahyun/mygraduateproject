# mygraduateproject
raspberry pi 4 + ubuntu server 22.04 LTS + ros humble for slam with realsense lidar camera(L515)


# to use Remote Desktop Connection in UBUNTU SERVER 22, you should install ubuntu-desktop
//https://www.layerstack.com/resources/tutorials/How-to-install-Graphical-User-Interface-GUI-for-Ubuntu-22-Cloud-Servers


$ sudo apt install ubuntu-desktop -y

$ sudo apt install xrdp -y

$ sudo systemctl status xrdp

//open your ubuntu server port 

$ sudo ufw allow 22

$ sudo ufw reload

$ sudo ufw status

$ sudo reboot

from now on, you can connect your ubuntu server to your Remote Desktop Connection(the basic app that windows gives)

# make swap size bigger
//make swap size bigger -> you should make swap size 2GB to use realsense-viewer
https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-22-04


<Step 3 – Creating a Swap File>

$ sudo fallocate -l 2G /swapfile

$ ls -lh /swapfile

<Step 4 – Enabling the Swap File>

$ sudo chmod 600 /swapfile

$ ls -lh /swapfile

$ sudo mkswap /swapfile

$ sudo swapon /swapfile

$ sudo swapon --show

$ free -h

<Step 5 – Making the Swap File Permanent>

$ sudo cp /etc/fstab /etc/fstab.bak

$ echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# serial network communication disable a login shell when you connect to your raspberry pi 4 where ttyS0 (my serial0 name)
//to use serial network communication


$ sudo systemctl mask serial-getty@ttyS0.service


//unable to go into login shell when you connect serial0, look this link https://forums.raspberrypi.com/viewtopic.php?t=159984
# download the humble in ubuntu server 22 
//to download the humble in ubuntu server 22 
https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html

<set locale>

$ locale  # check for UTF-8

$ sudo apt update && sudo apt install locales
  
$ sudo locale-gen en_US en_US.UTF-8
  
$ sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
  
$ export LANG=en_US.UTF-8

$ locale  # verify settings

<Setup Sources>

$ sudo apt install software-properties-common
  
$ sudo add-apt-repository universe


$ sudo apt update && sudo apt install curl
  
$ sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg

$ echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null


<Install ROS 2 packages>
  
$ sudo apt update
  
$ sudo apt upgrade
  
$ sudo apt install ros-humble-desktop

<Environment setup>

$ source /opt/ros/humble/setup.bash

# realsense installation on ubuntu to use the realsense-viewer and wrapper for testing the example code
//https://answers.ros.org/question/363889/intel-realsens-on-ubuntu-2004-ros-noetic-installation-desription/
//it is for noetic, but it works in humble too 
////////////////installation///////////////
<preparations>
  
$ export REALSENSE_SOURCE_DIR=$HOME/projects/librealsense/
  
$ sudo apt-get install guvcview git libssl-dev libusb-1.0-0-dev pkg-config libgtk-3-dev
  
$ sudo apt-get install libglfw3-dev libgl1-mesa-dev libglu1-mesa-dev
  
$ git clone https://github.com/IntelRealSense/librealsense.git $REALSENSE_SOURCE_DIR
  
$ mkdir $REALSENSE_SOURCE_DIR/build
  
$ cd $REALSENSE_SOURCE_DIR/build

<build>
  
$ export REALSENSE_INSTALL_PREFIX=/opt/realsense
  
$ sudo mkdir -p $REALSENSE_INSTALL_PREFIX; 
  
$ sudo chown $USER:$USER -R $REALSENSE_INSTALL_PREFIX # not relay needed -> you could run _make_ followed by _sudo make install_
  
$cmake ../ -DFORCE_RSUSB_BACKEND=true -DBUILD_PYTHON_BINDINGS=false -DCMAKE_BUILD_TYPE=release -DBUILD_EXAMPLES=true -DBUILD_GRAPHICAL_EXAMPLES=true -DCMAKE_INSTALL_PREFIX=$REALSENSE_INSTALL_PREFIX
  
$ make install

<extend the ld search path>
  
$ sudo sh -c "echo $REALSENSE_INSTALL_PREFIX/lib > /etc/ld.so.conf.d/realsense.conf"
  
$ sudo ldconfig

<udev rules>
  
$ cd ~/projects/librealsense/
  
$ sudo cp config/99-realsense-libusb.rules /etc/udev/rules.d/99-realsense-libusb.rules && sudo udevadm control --reload-rules && udevadm trigger

<make cmake config avaliable>
  
$ echo "export realsense2_DIR=/opt/realsense/lib/cmake/realsense2" >> ~/.bashrc

<reboot>
  
$ sudo reboot
  
<start realsense-viewer>
  
$ /opt/realsense/bin/realsense-viewer



# install pyrealsense2

///////////////////////////////////////////////
//and you should build for pyrealsense camera
  
$ cd ~/librealsense/build

<for python2>
  
$ cmake .. -DBUILD_PYTHON_BINDINGS=bool:true -DPYTHON_EXECUTABLE=$(which python)

<for python3>
  
$ cmake .. -DBUILD_PYTHON_BINDINGS=bool:true -DPYTHON_EXECUTABLE=$(which python3)

//for example $ cmake .. -DBUILD_PYTHON_BINDINGS=bool:true -DPYTHON_EXECUTABLE=/usr/local/lib

$ make -j1
  
$ sudo make install

<add python path>
  
$ vim ~/.zshrc
  
export PYTHONPATH=$PYTHONPATH:/usr/local/lib

$ source ~/.zshrc

# start to use run the example codes

<change the library name importing>
when you want to test the python code in librealsense/wrappers/python/examples

<you should change code to run in python>
import pyrealsense2 as rs  ->  import pyrealsense2.pyrealsense2 as rs


<when you have python3 and ubuntu server 22 to use rpi.gpio, you should install rpi.gpio package>

$ sudo apt-get install python3-rpi.gpio

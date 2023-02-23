# mygraduateproject
raspberry pi 4 + ubuntu server 22.04 LTS + ros humble for slam with realsense lidar camera

//make swap size bigger -> you should make swap size 2GB to use realsense-viewer
https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-22-04

//to use serial network communication
sudo systemctl mask serial-getty@ttyS0.service
//unable to go into login shell when you connect serial0, look this link https://forums.raspberrypi.com/viewtopic.php?t=159984

//to download the humble in ubuntu server 22 
https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html

//https://answers.ros.org/question/363889/intel-realsens-on-ubuntu-2004-ros-noetic-installation-desription/
//it is for noetic, but it works in humble too 
////////////////installation///////////////
# preparations
export REALSENSE_SOURCE_DIR=$HOME/projects/librealsense/
sudo apt-get install guvcview git libssl-dev libusb-1.0-0-dev pkg-config libgtk-3-dev
sudo apt-get install libglfw3-dev libgl1-mesa-dev libglu1-mesa-dev
git clone https://github.com/IntelRealSense/librealsense.git $REALSENSE_SOURCE_DIR
mkdir $REALSENSE_SOURCE_DIR/build
cd $REALSENSE_SOURCE_DIR/build

# build
export REALSENSE_INSTALL_PREFIX=/opt/realsense
sudo mkdir -p $REALSENSE_INSTALL_PREFIX; 
sudo chown $USER:$USER -R $REALSENSE_INSTALL_PREFIX # not relay needed -> you could run _make_ followed by _sudo make install_
cmake ../ -DFORCE_RSUSB_BACKEND=true -DBUILD_PYTHON_BINDINGS=false -DCMAKE_BUILD_TYPE=release -DBUILD_EXAMPLES=true -DBUILD_GRAPHICAL_EXAMPLES=true -DCMAKE_INSTALL_PREFIX=$REALSENSE_INSTALL_PREFIX
make install

# extend the ld search path
sudo sh -c "echo $REALSENSE_INSTALL_PREFIX/lib > /etc/ld.so.conf.d/realsense.conf"
sudo ldconfig

# udev rules
cd ~/projects/librealsense/
sudo cp config/99-realsense-libusb.rules /etc/udev/rules.d/99-realsense-libusb.rules && sudo udevadm control --reload-rules && udevadm trigger

# make cmake config avaliable
echo "export realsense2_DIR=/opt/realsense/lib/cmake/realsense2" >> ~/.bashrc

# reboot

# start realsense-viewer
/opt/realsense/bin/realsense-viewer



# install pyrealsense2

///////////////////////////////////////////////
//and you should build for pyrealsense camera
$ cd ~/librealsense/build

for python2
$ cmake .. -DBUILD_PYTHON_BINDINGS=bool:true -DPYTHON_EXECUTABLE=$(which python)

for python3
$ cmake .. -DBUILD_PYTHON_BINDINGS=bool:true -DPYTHON_EXECUTABLE=$(which python3)

//for example $ cmake .. -DBUILD_PYTHON_BINDINGS=bool:true -DPYTHON_EXECUTABLE=/usr/local/lib
$ make -j1
$ sudo make install

add python path
$ vim ~/.zshrc
export PYTHONPATH=$PYTHONPATH:/usr/local/lib

$ source ~/.zshrc



when you want to test the python code in librealsense/wrappers/python/examples

you should change code to run
import pyrealsense2 as rs  ->  import pyrealsense2.pyrealsense2 as rs


# Unitree RoBoCom

A demo and its description for Unitree RoBoCom.

## Dependencies
* [pyrealsense2](https://github.com/IntelRealSense/librealsense)
* [Opencv](https://opencv.org/)
* [msgpack](https://msgpack.org/)

## Installation Tips

### pyrealsense2 
1. Install dependencies
   ```bash
   sudo apt-get update && sudo apt-get -y upgrade
   
   sudo apt-get install -y --no-install-recommends python3-setuptools python3-pip python3-dev
   
   sudo apt-get install -y git libssl-dev libusb-1.0-0-dev pkg-config libgtk-3-dev
   
   sudo apt-get install -y libglfw3-dev libgl1-mesa-dev libglu1-mesa-dev
   ```
2. Clone
   ```bash
   git clone https://github.com/IntelRealSense/librealsense.git

   cd ./librealsense
   ```

3. Install
   ```bash
   sudo ./scripts/setup_udev_rules.sh
   
   mkdir build && cd build
   
   # Need Internet
   cmake ../ -DBUILD_PYTHON_BINDINGS:bool=true -DPYTHON_EXECUTABLE=/usr/bin/python3
   
   # Takes long time
   make -j2
   
   sudo make install
   
   sudo gedit ~/.bashrc
   
   # Insert the following line at the bottom of the file, then save and exit
   export PYTHONPATH=$PYTHONPATH: /usr/lib/python3/dist-packages/pyrealsense2
   
   # Source
   source ~/.bashrc

### Opencv
```bash
pip3 install --upgrade opencv-python
```

### msgpack
```bash
sudo apt-get install libmsgpack*
```
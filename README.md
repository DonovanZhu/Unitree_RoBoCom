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
   
   # This step only for raspberry pi:
   sudo ./scripts/setup_udev_rules.sh
 
   mkdir build && cd build
   
   # Need Internet
   cmake ../ -DFORCE_RSUSB_BACKEND=ON -DBUILD_PYTHON_BINDINGS:bool=true -DPYTHON_EXECUTABLE=/usr/bin/python3
   
   # Takes long time
   make -j2
   
   sudo make install
   
   sudo gedit ~/.bashrc
   
   # Insert the following line at the bottom of the file, then save and exit
   # If you are using Raspberry pi:
   export PYTHONPATH=$PYTHONPATH:/usr/lib/python3/dist-packages/pyrealsense2

   # If you are using Jetson NX:
   export PYTHONPATH=$PYTHONPATH:/usr/local/lib:/usr/local/lib/python3.6/pyrealsense2
   
   # Source
   source ~/.bashrc

### Opencv
```bash
# For Raspberry pi
pip3 install -U numpy
pip3 install -U opencv-python

# For Jetson NX
sudo apt-get install python3-opencv
```

### msgpack (on Up-board)
```bash
sudo apt-get install libmsgpack*
```

## Demo introduction
In this repo, four scripts are provided in "demo" folder. All the scripts are required to use on Unitree A1 robot for RoBoCom competition. The demo is a simple example for the first challenge in the competition, which is the color block detection.

[Unitree A1 robot](https://www.unitree.com/cn/products/a1) is a quadrupedal robot with high dynamics performance developed by [Unitree Robotics](https://www.unitree.com/cn). In the first challenge, a board with different color is shown on one side of a room. The user should program on A1 robot so that the robot is able to independently find the specific color on the board and touch the color block with its head. 


On A1 robot, an [Intel Up-board](https://up-board.org/) is implemented for high-level locomotion control and a [Raspberry pi 4b](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/) is used for solving [Realsense camera](https://www.intelrealsense.com/depth-camera-d435/) image and other sensor data. In the demo folder, Python scripts are used on Raspberry pi to handle the camera image and send motion control data to Up-board through UDP. According to the data from Raspberry pi, Up-board control the robot to complete the challenge.

### Image Processing
To detect specific color, running Color_Detection.py on Raspberry pi. It detects specific color, and draw rectengular contours around the color blocks. Then it selects the rightmost contour in the lens, and sends the center X coordinate of the contour to Up-board via UDP.

To find specific color, before running Color-Detection.py, user should pre-detect the HSV of the color through Realsense camera. First, running Realsense_Get_Picture.py to take a photo of the board. After holding the camera for few seconds, a image named "color_pick.jpg" will be saved in the folder. Then running GetHSV.py, this image will be shown in a window. By clicking the color block with mouse, a HSV list is provided in the output terminal.

After successfully get the HSV of the chosen color, edit the following line in Color_Detection.py according to the HSV value you get:

```bash
color_lower = np.array([55, 150, 140], np.uint8) 
color_upper = np.array([65, 165, 155], np.uint8)
```
These numbers are lower and upper boundery of HSV color you want to detect. The detected HSV value should be inside this range.

Now running Color_Detection.py to process the image.

### Motion Control
The motion control of A1 robot is based on the [unitree_legged_sdk](https://github.com/unitreerobotics/unitree_legged_sdk) developed by Unitree Robotics. Download [version 3.3](https://github.com/unitreerobotics/unitree_legged_sdk/releases/tag/v3.3), and unzip. Then install the dependencies according to the introduction of [unitree_legged_sdk](https://github.com/unitreerobotics/unitree_legged_sdk). 

To use color_detection.cpp, copy this file into the /unitree_legged_sdk-3.3/examples folder. Then edit CmakeLists.txt file:

```bash
# insert the following line 
add_executable(color_detection examples/color_detection.cpp)
target_link_libraries(color_detection ${EXTRA_LIBS})

```

Then cmake and make the project again, an executable file named ./color_detection will exist in /build folder. Running this file to control the robot.

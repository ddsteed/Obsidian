# ROS1
## INSTALL
### MAC
#### robostack
为了方便在mac下安装ROS，Wolf Vollprecht和Tobias Fischer利用conda发起了一个project，制作了跨平台的ROS安装方案[^robostack-turtlesim]。请参考：https://medium.com/robostack/cross-platform-conda-packages-for-ros-fa1974fd1de3 和 https://github.com/RoboStack, 以及作者主页：https://robostack.github.io/index.html

如果不愿意一步一步跟着来，作者甚至贴心地集成了一个命令：`conda create -n ros ros-noetic-desktop -conda-forge -c robostack` 。执行完之后，就会生成一个全新的虚拟环境**ros**，包含所有的ros-noetic指令。[^robostack-turtlesim]

#### from source
Step | Command
--------- | ------------
1. 创建conda虚拟环境 | `conda create -n rosenv` 
2. 进入虚拟环境 | `conda activate rosenv`
3. 安装pip | `conda install pip`
4. 安装rosdep程序 | `pip3 install -U rosdep rosinstall_generator vcstool`
5. 升级setuptools | `pip3 install --upgrade setuptools`
6. 初始化rosdep | `sudo rosdep init`
7. 升级rosdep  | `rosdep update`
8. 创建自己的catkin工作空间 | `mkdir $path/catkin_ws && cd $path/catkin_ws`
9. 下载ros安装源代码文件 | `rosinstall_generator desktop --rosdistro noetic --deps --tar > noetic-desktop.rosinstall`   `mkdir ./src` `vcs import --input noetic-desktop.rosinstall ./src`
10. 核查安装需要的各种库及依赖关系 | `rosdep install --from-paths ./src --ignore-packages-from-source --rosdistro noetic -y` [^macver][^library]
11. 编译源代码 | `./src/catkin/bin/catkin_make_isolated --install -DCMAKE_BUILD_TYPE=Release`[^cmake] [^clang++]

### Ubuntu
#### apt
可以参照 https://wiki.ros.org/noetic/Installation/Ubuntu, 安装步骤，就可以在Ubuntu上安装好_ROS-noetic_。
#### from source
Step | Command
--------- | ------------
1. (Ubuntu) 安装依赖文件 | `sudo apt-get install python3-rosdep python3-rosinstall-generator python3-vcstool build-essential`
2. (Generic) 安装依赖文件 | ` sudo pip3 install -U rosdep rosinstall_generator vcstool` `sudo pip3 install --upgrade setuptools`
3. 初始化rosdep | `sudo rosdep init`
4.  升级rosdep  | `rosdep update`
5.  创建自己的catkin工作空间 | `mkdir $path/catkin_ws && cd $path/catkin_ws`
6.  下载ros安装源代码文件 | `rosinstall_generator desktop --rosdistro noetic --deps --tar > noetic-desktop.rosinstall`   `mkdir ./src` `vcs import --input noetic-desktop.rosinstall ./src`
7.  核查并安装需要的各种库及依赖关系 | `rosdep install --from-paths ./src --ignore-packages-from-source --rosdistro noetic -y` 
8.  编译catkin工作空间 | ` ./src/catkin/bin/catkin_make_isolated --install -DCMAKE_BUILD_TYPE=Release`
9.  初始化变量 | `source ~/ros_catkin_ws/install_isolated/setup.zsh`

# ROS2
## ROS1 vs. ROS2
[ROS1 vs ROS2, Practical Overview For ROS Developers](https://roboticsbackend.com/ros1-vs-ros2-practical-overview/) 介绍了使用ROS1和ROS2的不同原则，开发者尤其需要认真读一读该文。


[^robostack-turtlesim]: 这个方法安装很简单，但经我测试，安装完之后，`rosrun turtlesim turtlesim_node` 没有窗口和turtle出现（Ubuntu下是正常的）。直到2021.11.05，作者也没有给出解决方案。

[^macver]: 如果出现"ERROR: Rosdep experienced an error: unrecognized version: 12"，表明操作系统版本号没有被认出，可以修改“/Users/opt/miniconda3/envs/rosenv/lib/python3.9/site-packages/rospkg/os_detect.py”，找到相应的操作系统名称，添加上本机的即可。

[^library]:如果使用conda建立的基本虚拟环境，大致需要安装的库包括：a) 需要用mamba安装的有：**empy、boost、console_bridge、poco、tinyxml2、tinyxml、qt、sip、pyqt、gtest、gmock、orocos-kdl、urdfdom、log4cxx、**

[^cmake]: ROS采用cmake来编译系统，有时会出现找不到某个库的情况。例如：Poco库通过conda安装在`/opt/miniconda3/envs/rosenv/`下面，但是编译的时候找不到。这时需要把cmake的include和lib路径显示地指定下来：`export CMAKE_INCLUDE_PATH=/Users/opt/miniconda3/envs/rosenv/include:${CMAKE_INCLUDE_PATH}`以及`export CMAKE_LIBRARY_PATH=/opt/miniconda3/envs/rosenv/lib:${CMAKE_LIBRARY_PATH}`

[^clang++]: 有些C++源码采用的是c++11的标准，mac默认的编译器不支持。因此要重新制定编译器：`export CC=/usr/bin/gcc; export CXX="/usr/bin/g++"; export CXXFLAGS="-std=c++11"`


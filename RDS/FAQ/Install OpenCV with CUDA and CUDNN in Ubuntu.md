# Jetson
[How to Install OpenCV with CUDA Support on Jetson Modules?](https://www.forecr.io/blogs/installation/how-to-install-opencv-with-cuda-support)

[OpenCV 4 + CUDA on Jetson Nano](https://jetsonhacks.com/2019/11/22/opencv-4-cuda-on-jetson-nano/)


# General Ubuntu
https://gist.github.com/raulqf/f42c718a658cddc16f9df07ecc627be7

First of all install update and upgrade your system:

```bash
sudo apt update

sudo apt upgrade
```

Then, install required libraries:

-   Generic tools:
    
    ```bash
    sudo apt install build-essential cmake pkg-config unzip yasm git checkinstall
    ```
    
-   Image I/O libs
    
    ```bash
    sudo apt install libjpeg-dev libpng-dev libtiff-dev
    ```
    
-   Video/Audio Libs - FFMPEG, GSTREAMER, x264 and so on.
    
    ```bash
    sudo apt install libavcodec-dev libavformat-dev libswscale-dev libavresample-dev
    
    sudo apt install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev
    
    sudo apt install libxvidcore-dev x264 libx264-dev libfaac-dev libmp3lame-dev libtheora-dev 
    
    sudo apt install libfaac-dev libmp3lame-dev libvorbis-dev
    ```
    
-   OpenCore - Adaptive Multi Rate Narrow Band (AMRNB) and Wide Band (AMRWB) speech codec
    
    ```bash
    sudo apt install libopencore-amrnb-dev libopencore-amrwb-dev
    ```
    
-   Cameras programming interface libs
    
    ```bash
    sudo apt-get install libdc1394-22 libdc1394-22-dev libxine2-dev libv4l-dev v4l-utils
    
    cd /usr/include/linux
    sudo ln -s -f ../libv4l1-videodev.h videodev.h
    cd ~
    ```
    
-   GTK lib for the graphical user functionalites coming from OpenCV highghui module
    
    ```bash
    sudo apt-get install libgtk-3-dev
    ```
    
-   Python libraries for python3:
    
    ```bash
    sudo apt-get install python3-dev python3-pip
    sudo -H pip3 install -U pip numpy
    sudo apt install python3-testresources
    ```
    
-   Parallelism library C++ for CPU
    
    ```bash
    sudo apt-get install libtbb-dev
    ```
    
-   Optimization libraries for OpenCV
    
    ```bash
    sudo apt-get install libatlas-base-dev gfortran
    ```
    
-   Optional libraries:
    
    ```bash
    sudo apt-get install libprotobuf-dev protobuf-compiler
    
    sudo apt-get install libgoogle-glog-dev libgflags-dev
    
    sudo apt-get install libgphoto2-dev libeigen3-dev libhdf5-dev doxygen
    ```
    

We will now proceed with the installation (see the Qt flag that is disabled to do not have conflicts with Qt5.0).

```bash
cd ~/Downloads

wget -O opencv.zip https://github.com/opencv/opencv/archive/refs/tags/4.5.2.zip

wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/refs/tags/4.5.2.zip

unzip opencv.zip
unzip opencv_contrib.zip

echo "Create a virtual environtment for the python binding module (OPTIONAL)"
sudo pip install virtualenv virtualenvwrapper
sudo rm -rf ~/.cache/pip
echo "Edit ~/.bashrc"
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
source /usr/local/bin/virtualenvwrapper.sh
mkvirtualenv cv -p python3
pip install numpy

echo "Procced with the installation"
cd opencv-4.5.2
mkdir build
cd build

# numpy should be installed firstly, or the cv2 could NOT be installed correctly
cmake \
        -D CMAKE_BUILD_TYPE=Release \
        -D CMAKE_C_COMPILER=/usr/bin/gcc \
        -D CMAKE_CXX_COMPILER=/usr/bin/g++ \
        -D BUILD_opencv_face=ON \
        -D CUDA_GENERATION=Auto \
        -D CUDA_FAST_MATH=ON \
        -D WITH_CUDA=ON \
        -D WITH_CUDNN=ON \
        -D CUDA_ARCH_BIN=8.6 \
        -D OPENCV_DNN_CUDA=ON \
        -D WITH_CUBLAS=ON \
        -D WITH_TBB=ON \
        -D BUILD_NEW_PYTHON_SUPPORT=ON \
        -D WITH_V4L=ON \
        -D WITH_QT=OFF \
        -D WITH_PROTOBUF=OFF \
        -D WITH_OPENGL=ON \
        -D BUILD_opencv_python3=ON \
        -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
        -D PYTHON_EXECUTABLE=$(which python) \
        -D PYTHON_INCLUDE_DIR=$(python -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
        -D PYTHON_PACKAGES_PATH=$(python -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") \
        -D CMAKE_INSTALL_PREFIX=$(python -c "import sys; print(sys.prefix)") \
        -D OPENCV_PYTHON3_INSTALL_PATH=$(python -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())") \
        ..

```

If you want to build the libraries statically you only have to include the _-D BUILD_SHARED_LIBS=OFF_

In case you do not want to include include CUDA set _`-D WITH_CUDA=OFF_`

If you want also to use CUDNN you must include those flags (to set the correct value of CUDA_ARCH_BIN you must visit [https://developer.nvidia.com/cuda-gpus](https://developer.nvidia.com/cuda-gpus) and find the Compute Capability CC of your graphic card). If you have problems with the setting up of CUDDN check the _List of documented problems_:

```bash
-D WITH_CUDNN=ON \
-D OPENCV_DNN_CUDA=ON \
-D CUDA_ARCH_BIN=8.6 \
```

Before the compilation you must check that CUDA has been enabled in the configuration summary printed on the screen. (If you have problems with the CUDA Architecture go to the end of the document).

```
--   NVIDIA CUDA:                   YES (ver 11.7, CUFFT CUBLAS FAST_MATH)
--     NVIDIA GPU arch:             86
--     NVIDIA PTX archs:
-- 
--   cuDNN:                         YES (ver 8.5.0)

```

I've included below the output of my [configuration](https://gist.github.com/raulqf/f42c718a658cddc16f9df07ecc627be7#configuration-information).

If it is fine proceed with the compilation (Use nproc to know the number of cpu cores):

```bash
$ nproc
$ make -j8
$ sudo make install
```

Include the libs in your environment

```bash
sudo /bin/bash -c 'echo "/usr/local/lib" >> /etc/ld.so.conf.d/opencv.conf'

sudo ldconfig
```

If you want to have available opencv python bindings in the system environment you should copy the created folder during the installation of OpenCV (* -D OPENCV_PYTHON3_INSTALL_PATH=~/.virtualenvs/cv/lib/python3.8/site-packages *) into the _dist-packages_ folder of the target python interpreter:

````bash
sudo cp -r ~/.virtualenvs/cv/lib/python3.8/site-packages/cv2 /usr/local/lib/python3.8/dist-packages

echo "Modify config-3.8.py to point to the target directory" 
sudo nano /usr/local/lib/python3.8/dist-packages/cv2/config-3.8.py 

``` 
    PYTHON_EXTENSIONS_PATHS = [
    os.path.join('/usr/local/lib/python3.8/dist-packages/cv2', 'python-3.8')
    ] + PYTHON_EXTENSIONS_PATHS
``` 
````
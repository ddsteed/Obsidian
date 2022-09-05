https://bytefreaks.net/applications/compiling-ffmpeg-with-nvidia-gpu-hardware-acceleration-on-ubuntu-20-04lts

Please note that the following commands were executed on a system that already had CUDA support so we might be missing a step or two to enable NVIDIA CUDA support.

# Install necessary packages
```bash
sudo apt-get install build-essential yasm cmake libtool libc6 libc6-dev unzip wget libnuma1 libnuma-dev nvidia-cuda-toolkit libass-dev libfdk-aac-dev libmp3lame-dev
```

# Install ffnvcodec
```bash
git clone https://git.videolan.org/git/ffmpeg/nv-codec-headers.git

cd nv-codec-headers

sudo make install

cd -
```

# Compile FFmpeg

```bash
git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg/

cd ffmpeg

./configure --enable-nonfree --enable-cuda-nvcc --enable-libnpp --extra-cflags=-I/usr/local/cuda/include --extra-ldflags=-L/usr/local/cuda/lib64 --disable-static --enable-shared
```
or 
```bash
../configure \
  --enable-cuda-nvcc \
  --enable-cuvid \
  --enable-libnpp \
  --extra-cflags="-I/usr/local/cuda/include/" \
  --extra-ldflags=-L/usr/local/cuda/lib64/ \
  --enable-nvenc \
  --enable-libass \
  --disable-debug \
  --enable-libvorbis \
  --enable-libvpx \
  --enable-opencl \
  --enable-gpl \
  --cpu=native \
  --enable-libfdk-aac \
  --enable-libx264 \
  --enable-libx265 \
  --enable-openssl \
  --enable-librtmp \
  --extra-libs="-lpthread -lm -lz" \
  --enable-nonfree
```
if some library is missed, `apt-get install` it.

```bash
make -j 8

sudo make install

```
# Update library 
```bash
sudo ldconfig
```

# SUCCESS

After performing the above steps, we were able to process media using `ffmpeg` without stressing our CPU! The workload was transferred to the GPU!
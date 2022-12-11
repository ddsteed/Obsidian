# 安装 nginx 服务

[Linux-Nginx+rtmp+ffmpeg 搭建流媒体服务器](https://blog.51cto.com/u_13710166/5272848)

## prerequisite modules
- nginx-core 
- nginx-common 
- libnginx-mod-rtmp

## install
```bash
sudo apt-get install nginx-core nginx-common libnginx-mod-rtmp
```

## 修改配置文件
```bash
# vim nginx.conf  
rtmp {  
	server {  
	listen 1935; #监听的端口号  
	application stream { #自定义的名字: 这里的stream可以换成任意的名字，但后面推送的地址必须与之符合  
		live on;  
	}  
		application hls {  
			live on;  
			hls on;  
			hls_path /tmp/hls;  # 这里的地址是保存hls的地方，可以修改
			hls_fragment 1s;  
			hls_playlist_length 3s;  
		}  
	}  
}  
# /etc/init.d/nginx reload
```

### 完整的 nginx.conf 示例
```bash
user root;  
worker_processes 4;  
worker_cpu_affinity 1000 0100 0010 0001;  
worker_rlimit_nofile 30000;  
  
  
#error_log logs/error.log warn;  
pid /var/run/nginx.pid;  
  
  
events {  
worker_connections 30000;  
use epoll;  
}  
  
rtmp {  
server {  
listen 1935;  
application myapp {  
live on;  
}  
application hls {  
live on;  
hls on;  
hls_path /tmp/rtmp/hls;  
hls_fragment 1s;  
hls_playlist_length 3s;  
}  
}  
}  
  
  
http {  
  
limit_req_zone $binary_remote_addr zone=req_one:10m rate=1r/s;  
  
autoindex off;  
include /opt/nginx-1.9.5/conf/mime.types;  
default_type application/octet-stream;  
  
log_format main '$remote_addr [$time_local] "$request" '  
'$status $body_bytes_sent "$http_referer" '  
'"$http_user_agent" $http_x_forwarded_for $request_length $request_time "$upstream_response_time" "$upstream_addr" $host $server_addr';  
  
  
  
#access_log logs/access.log main;  
server_tokens off;  
sendfile on;  
#tcp_nopush on;  
  
keepalive_timeout 65;  
  
gzip on;  
gzip_min_length 1K;  
gzip_buffers 4 16K;  
gzip_comp_level 8;  
gzip_http_version 1.0;  
gzip_types application/json text/javascript text/plain application/x-javascript text/css application/xml text/xml;  
#gzip_types application/json text/javascript text/plain application/x-javascript text/css application/xml text/xml image/jpeg image/gif image/png;  
gzip_disable "MSIE [1-6]\.";  
client_max_body_size 500M;  
  
  
#server {  
# listen 80 default;  
# return 500;  
#}  
  
include /opt/nginx-1.9.5/conf/conf.d/*.conf;  
  
  
}
```

## 重启 nginx 服务
```bash
 sudo service nginx restart  
 ```
 
 
 # 安装 ffmpeg with GPU
 
 [compile ffmpeg](https://docs.nvidia.com/video-technologies/video-codec-sdk/ffmpeg-with-nvidia-gpu/)
 
## FFmpeg on Linux
- Clone ffnvcodec
```bash
git clone https://git.videolan.org/git/ffmpeg/nv-codec-headers.git
```

- Install ffnvcodec
```bash
cd nv-codec-headers && sudo make install && cd –
```

- Clone FFmpeg's public GIT repository.
```bash
git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg/
```

- Install necessary packages.
```bash
sudo apt-get install build-essential yasm cmake libtool libc6 libc6-dev unzip wget libnuma1 libnuma-dev
```

- Configure
```bash
./configure --enable-nonfree --enable-cuda-nvcc --enable-libnpp --extra-cflags=-I/usr/local/cuda/include --extra-ldflags=-L/usr/local/cuda/lib64 --disable-static --enable-shared
```

- Compile
```bash
make -j 8
```

- Install the libraries.
```bash
sudo make install
```
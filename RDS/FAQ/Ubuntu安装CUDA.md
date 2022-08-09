[https://www.cnblogs.com/booturbo/p/13960935.html](https://www.cnblogs.com/booturbo/p/13960935.html)  

# 下载
首先在Nvidia官网下载适合自己机器的CUDA版本，[官网下载](https://developer.nvidia.com/CUDA-TOOLKIT-ARCHIVE)，本次使用 **runfile** 的方式安装（用deb格式安装的话，会在安装过程中替换掉已经安装好的显卡驱动），如图所示：

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111210217633-615558339.png)

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111210238803-1554783045.png)

在终端输入：  **wget**https://developer.download.nvidia.com/compute/cuda/11.0.3/local_installers/**cuda_11.0.3_450.51.06**_linux.run 

# 安装
下载完成后输入： 
```bash
sudo sh cuda_11.0.3_450.51.06_linux.run  
```

如果电脑没有安装Nvidia显卡驱动，安装会顺利进行；若已经安装了Nvidia驱动，会提醒移除，此时我们先Abort，如下图，

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111211818978-91773005.png)

用一个简单的方法移除已安装的Nvidia驱动，在Software & Updates里面找到Additional Drivers，选择Ubuntu系统自带的驱动，Apply changes后，重启，如下图

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111213341264-1391598520.png)

重新输入： 
```bash
sudo sh cuda_11.0.3_450.51.06_linux.run 
```

下面界面中输入 **accept**  回车，

**注意：如果按照上面的方式移除了Nvidia驱动，还是出现提示，那就选择Continue，然后在下图取消安装Driver，[X]  Driver   --->   [ ]  Driver ，只要已安装的驱动版本足够高即可。**

**![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111214215316-105781230.png)**

下面选择 **Install**，

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111214657165-1402061154.png)

安装完成，显示如下图，

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111214834116-637104923.png)

# 配置变量
根据提示的信息，我们需要配置环境变量，打开 bashrc 文件，

输入： **sudo** gedit ~/.bashrc  ，然后在文件末尾添加下面3行（第1行是注释），保存
```bash
# CUDA Soft Link
export PATH=/usr/local/cuda-11.0/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-11.0/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

然后刷新环境变量，输入： `source ~/.bashrc `

# 测试

输入：  **nvcc** -V  ，显示如下图，说明成功，

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111220938726-1559799783.png)

# 安装cuDNN
[官网下载](https://developer.nvidia.com/rdp/cudnn-download?spm=a2c4e.10696291.0.0.1df819a4HJWSTe)（未注册的话，注册一个账号即可），所需下载包如下图

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111221717195-581926566.png)

将下载的 cuDNN Library for Linux (x86_64) 解压，复制解压出来的文件到安装好的CUDA环境中，

输入：  
```bash
sudo cp cuda/include/cudnn.h /usr/local/cuda/include 

sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64 
```

更改权限输入： 
```bash
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn
```

接下来安装Deb包， cuDNN Runtime Library for Ubuntu20.04(Deb)，cuDNN Developer Library for Ubuntu20.04(Deb)，cuDNN Code Samples and User Guide for Ubuntu20.04(Deb)

分别输入：
```bash
sudo dpkg -i libcudnn8_8.0.5.39-1+cuda11.0_amd64.deb 

sudo dpkg -i libcudnn8-dev_8.0.5.39-1+cuda11.0_amd64.deb   

sudo dpkg -i libcudnn8-samples_8.0.5.39-1+cuda11.0_amd64.deb
```

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111224322498-1199988350.png)

安装结束后重启，测试是否安装成功，

方法1，输入： 
```bash
cp -r /usr/src/cudnn_samples_v8/ ~  

cd ~/cudnn_samples_v8/mnistCUDNN/  

make clean && make 

./mnistCUDNN
```

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111225740276-1470072201.png)

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111230450646-785854793.png)

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111230744878-482929379.png)

出现**Test passed**！没有报错即安装成功。

方法2，输入： 
```bash
cd /usr/local/cuda/samples/1_Utilities/deviceQuery  

sudo make  

./deviceQuery
```

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111231046806-844735961.png)

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201111231333629-694506473.png)

出现 **Result = PASS**，说明安装成功。

# 补充

Ubuntu系统安装更新后，显卡驱动切换成了系统自带的驱动，手动安装CUDA时安装的显卡驱动看不到了，

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201112222521729-1866813411.png)

此时，可以使用单独安装显卡驱动的办法来解决，

首先，更新apt-get 源列表

输入：
```bash
sudo apt-get update

sudo apt-get upgrade
```

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201112223454416-1836147113.png)

接着，添加驱动到库并更新

输入： 
```bash
sudo add-apt-repository ppa:graphics-drivers/ppa  

sudo apt-get update  
```


![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201112223932049-183758394.png)

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201112223948462-815169907.png)

然后就可以在Additional Drivers里面找到适合的较新的驱动版本了，选择对应的驱动，点击Apply changes，等待安装完成后，重启即可。

![](https://img2020.cnblogs.com/blog/1585117/202011/1585117-20201112224945049-2020079486.png)
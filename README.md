# 服务器端配置

1. 参考客户端教程，先连接服务器公用账户，来配置自己的私有账户

   ```
   ssh user@<YOURSERVERIP>
   ```

2. 新建用户, <USERNAME>是你自定义的用户名

   ```
   sudo useradd -g <MAINUSERNAME> -s /bin/bash -m <USERNAME>
   sudo passwd <USERNAME>
   ```

3. 切换用户

   ```
   su - <USERNAME>
   ```

4. 数据集和公共资源存储在机械硬盘

   ```
   mkdir /data/YOURFILENAME #新建文件夹
   sudo chmod 777 /data/YOURFILENAME #将你的文件夹权限改为全部可读可写
   ```

5. 更改权限

   **注意：**此处是修改你的用户home的读写权限，为了安全给大家设成了754，但这样在我们从客户端向服务器传数据的时候会稍微麻烦一点。嫌麻烦的同学请直接设成777

   ```
   sudo chmod 754 /home/<USERNAME> #用户rwx;组rx;其他组r
   ```

6. 生成SSH密钥

   ```
   ssh-keygen -t rsa
   cd /home/<USERNAME>/.ssh
   ls
   # 并将客户端公钥.pub拷贝至上文件夹，重命名为authorized_keys
   ```

7. 可选：拷贝路径下的公钥 id_rsa.pub 中内容，复制并修改文件名为authorized_keys到服务器~\\.ssh中。可以避免每次都要输密码

8. (Anaconda已经安装好，在/home/anaconda3路径下，安装方案参见下文)配置conda环境变量

   ```
   echo 'export PATH="/home/anaconda3/bin:$PATH"' >> ~/.bashrc
   source ~/.bashrc
   conda init
   ```

9. 重启终端

10. 参照下文配置清华源
    https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/

11. 按需配置开发环境, ENVNAME是你自定义的虚拟环境名

    ```
    conda create -n ENVNAME python=3.8
    conda activate ENVNAME
    conda install pytorch torchvision torchaudio cudatoolkit=11.1 -c pytorch-lts -c nvidia
    ```

12. 配置cuda路径

    ```
    echo 'export PATH="/usr/local/cuda-11.1/bin:$PATH"' >> ~/.bashrc
    source ~/.bashrc
    ```

13. 此后可以在客户端连接我们自己的账户

    ````
    ssh <USERNAME>@<YOURSERVERIP>
    ````

# 客户端配置（windows为例）

## 客户端设置SSH

1. 设置→程序→搜索“添加可选功能”→添加功能→安装openssh

2. 打开终端输入，生成SSH公钥和私钥在路径C:\Users\dell\\.ssh

   ```
   ssh-keygen -t rsa -b 4096
   ```

3. 验证连通性

   ```
   ssh <USERNAME>@YOURSERVERIP
   exit
   ```

## VSCode设置

1. 在左侧拓展处搜索remote -SSH，下载安装；搜索python，下载安装

2. 打开安装的“远程资源管理器”，选择SSH Target，点+号配置新连接 

   ```
   ssh <USERNAME>@YOURSERVERIP
   ```

   配置会以文件存储，一般选择默认的存储路径即可

3. 点齿轮，选择配置文件可以个性化修改

4. 连接：

   - 远程资源管理器→SSH Target，点击/右键连接

   - 如果没有把公钥上传服务器的话，需要输入用户密码；

   - 如果已上传服务器，可直接建立连接；

## PyCharm开发环境设置

见后第4章节

## 客户端把文件复制到服务器（ubuntu）

1. 赋予当前客户端用户待复制的文件/文件夹读取权限

   ```
   # 注意分别在客户端和服务器端操作，读写权限已经设成777的可忽略本步骤
   sudo chmod 777 local_file_path #ubuntu客户端,windows可忽略
   sudo chmod 777 remote_file_path #服务器
   ```

2. 传输

   ```
   scp -r local_file_path <USERNAME>@<YOURSERVERIP>:remote_file_path #文件夹
   scp local_file_path <USERNAME>@<YOURSERVERIP>:remote_file_path #文件
   ```

3. 设置回原读写权限

   ```
   # 注意分别在客户端和服务器端操作
   sudo chmod 754 local_file_path #ubuntu客户端,windows可忽略
   sudo chmod 754 remote_file_path #服务器
   ```

## 非常重要

1. 考虑到系统存储空间分配，将代码和数据分开存放

   ```
   数据存储路径：/data/<USERNAME>（机械盘）
   代码存储路径：/home/<USERNAME>/WorkSpace（固态盘）
   
   # 上述两个路径已经帮师兄建好了，数据的存储路径师兄没有特殊需求不要改，代码路径（或者说/home/FMX/）下的内容随便你折腾
   ```

2. 基本上大部分工作都可以在自己的用户目录下完成，最好不用sudo

3. 注意数据安全，所有的权限都写成 777 是绝对不安全的！及时备份， 及时改权限！

4. 服务器属于实验室公共资源，**请勿将密码等信息外传**，导致中挖矿病毒或文件损失

5. 重启电脑之后，请选择user用户登陆，我们的远程控制软件安装在user用户下

# 开发环境配置

## Anaconda

在清华源下载[Anaconda3-2021.11-Linux-x86_64.sh](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2021.11-Linux-x86_64.sh)

安装：

```
bash Anaconda3-2021.11-Linux-x86_64.sh
按照指引安装，注意需要指定安装路径 /home/anaconda3
-----
对个人用户来说，需要用的时候
添加环境变量
方法1: vim ~/.bashrc 在结尾加上一行 export PATH="/home/anaconda3/bin:$PATH"
方法2: echo 'export PATH="/home/anaconda3/bin:$PATH"' >> ~/.bashrc
```

## PyTorch

进入预定的虚拟环境后，执行下述语句即可

```
conda install pytorch torchvision torchaudio cudatoolkit=11.1 -c pytorch-lts -c nvidia
```

## tensorflow-gpu

**重要提醒：tensorflow-gpu、cuda、cudnn（、keras）版本号一定一定一定要对应，否则就要重新安装，特别麻烦！**建议按照本教程中的版本号来安装。

另外，本教程只适用于tensorflow1版本。

### 3.2.1 显卡驱动的安装

如果是在服务器上安装，由于服务器上已经安装了显卡驱动，因此这个步骤可以跳过。如果是自己的ubuntu系统，需要额外检查一下显卡驱动有没有安装。

### 3.2.2 新建环境

专门创建一个环境用来执行tensorflow程序，这样就可以避免拓展包等的冲突。环境名字可以自定义，假设我想创建的环境名字为tensorflow-gpu。

新建环境指令：

   ```
conda create -n tensorflow-gpu python=3.6
   ```

进入环境：

   ```
source activate tensorflow-gpu
   ```

另外，如果不做特殊说明，以下步骤全部在新建环境中执行。

### 3.2.3 安装tensorflow-gpu

   ```
pip install tensorflow-gpu==1.13.1
   ```

### 3.2.4 安装cuda（刘洋版本）

下载cuda安装包：访问 https://developer.nvidia.com/cuda-toolkit-archive

执行：sudo sh cuda_10.0.130_410.48_linux.run

注意两个需要输入路径的地方是CUDA安装路径，在后面配置环境的时候要用到。

安装好之后接下来是环境变量配置，首先执行：vim ~/.bashrc，在最后添加以下三句话（加粗的是上图中输入的CUDA安装路径）：

   ```
export PATH="/home/ZXL/CUDA/bin:$PATH" 

export LD_LIBRARY_PATH="/home/ZXL/CUDA/lib64:$LD_LIBRARY_PATH" 

export CUDA_HOME=“/home/ZXL/CUDA”
   ```

完成之后执行：source ~/.bashrc，然后再次进入你的环境，执行nvcc -V，查到cuda版本说明安装成功。

### 3.2.5 安装cudnn

访问https://developer.nvidia.com/rdp/cudnn-archive 

![image-20211201112844813](./README/2021-12-01-3.png)

 将安装包下载到本地，然后上传到服务器并解压。解压后生成的文件夹叫cuda，这个代表了cudnn的路径，不要与之前安装的CUDA路径搞混。为了方便理解，我将小写的cuda表示为cudnn，大写的CUDA表示之前安装CUDA的路径。

执行以下指令：

   ```
cp /home/ZXL/CUDA/cuda/include/cudnn.h /home/ZXL/CUDA/include
cp /home/ZXL/CUDA/cuda/lib64/libcudnn /home/ZXL/CUDA/cuda-9.0/lib64

chmod a+r /home/ZXL/CUDA/include/cudnn.h /home/ZXL/CUDA/lib64/libcudnn
   ```

### 3.2.6 验证是否成功

输入python，此时可以写python语句。输入：import tensorflow，按下回车，如果没有报错，则安装成功。

# 4、遇到的问题（仅记录，无需操作）

## 4.1 Failed to initialize NVML: Driver/library version mismatch

```
sudo dpkg --list | grep nvidia-* #查看nvidia显卡驱动版本
cat /proc/driver/nvidia/version #查看NVRM版本
# 出现上述问题的原因是：驱动自动更新，而内核还是旧的版本
## 卸载原驱动
sudo /usr/bin/nvidia-uninstall
sudo apt-get --purge remove nvidia-*
sudo apt-get purge nvidia*
sudo apt-get purge libnvidia*
## 重新安装
sudo chmod a+x NVIDIA-Linux-x86_64-460.84.run
sudo NVIDIA-Linux-x86_64-460.84.run -no-x-check -no-nouveau-check -no-opengl-files
sudo
## 禁用更新
sudo apt-mark hold nvidia-driver-460
```

## 4.2 机械硬盘自动挂载

```
df -h # 查看已挂载的硬盘，如果没有/dev/sda1, 也就是我们的机械盘（3.7TB）
sudo fdisk -l # 查看全部的硬盘
sudo mount /dev/sda1 /data #将机械盘挂载到/data下
sudo chmod 777 /data #解锁读写权限
----
自动挂载
sudo blkid #查看硬盘的UUID
sudo vim /etc/fstab
加入
UUID=cb7539fe-9191-471d-ba26-44323059667b /data ext4 defaults 0 1
```


## 4.3 切换lightdm图形界面（为了使用向日葵）

```
sudo apt install lightdm
sudo dpkg-reconfigure lightdm
```

# 5、PyCharm远控服务器教程

## 5.1 申请专业版PyCharm

PyCharm的编程体验和远控体验个人认为远超VSCode，推荐大家试试PyCharm

远程部署只有专业版professional才可以使用，用sdu邮箱可以申请教育试用，如下网址：

https://www.jetbrains.com/community/education/#students

（部分学校如中科大，矿大等学校由于edu邮箱是永久的，即毕业后邮箱不会收回。所以JetBrain收回了这些学校的邮箱申请通道，在校生需要以比较复杂的方式申请。）

## 5.2 部署远程服务器

主要包括配置<u>远程python解释器</u>和<u>远程工作区与本地工作区映射关系</u>两个步骤。

**PyCharm的远控逻辑：**在本地和云端同步两个内容一致的文件夹，本地的更改会实时映射到云端，使用云端服务器的python解释器执行代码。

### 5.2.1 几个推荐的插件

```
file→setting→plugin
或：文件→设置→插件
```

- Chinese(Simplified) Language Pack	汉化包
- CodeGlance3 类似于VSCode的右侧预览界面
- Material Theme UI 比较丰富的主题

### 5.2.2 配置SSH服务器

```
文件→设置→工具→SSH配置
```

![2021-11-30_163039](./README/2021-11-30_163039.png)

### 5.2.3 部署远程映射

1. 打开配置界面

   ```
   工具→部署→配置
   ```

![2021-11-30_162253](./README/2021-11-30_162253.png)

2. 点击“+”新建配置，选择SFTP

3. 选择我们配置好的ssh，配置远程服务器

   ![2021-11-30_164519](./README/2021-11-30_164519.png)

4. 配置映射关系（此处可参考下文Tips中”一个理想的模板“）

![2021-11-30_164734](./README/2021-11-30_164734.png)

选择**本地路径**和相应的**部署路径（即远程路径）**

**非常非常需要注意的事情！**

**非常非常需要注意的事情！**

**非常非常需要注意的事情！**

a. **\<部署\>→\<映射\>→\<部署路径\>** 指的是相对于**\<部署\>→\<连接\>→\<根路径\>**的相对路径。也就是说：

```
根路径：/
部署路径：/home/<USERNAME>/WorkSpace/...
或者
根路径：/home/<USERNAME>
部署路径：/WorkSpace/...
```

以上两个是指的同一种情况。（请自行学习Ubuntu的文件结构）

b. 当你在一个项目中，修改了根目录的话，在其他项目中也会更改。**每次切换项目务必查看一下文件的映射关系，不要上传错位置**

### 5.2.4 配置远程python解释器

1. 选择SSH解释器

```
文件→项目.Python解释器→添加SSH Python解释器→直接选择现有服务器配置
```

![2021-11-30_161745](./README/2021-11-30_161745.png)

2. 配置解释器路径

解释器路径参照下图，同步文件夹参照上

![2021-11-30_165916](./README/2021-11-30_165916.png)

### 5.2.5 Tips

**浏览远程主机**

```
工具→部署→浏览远程主机这样你就可以浏览到远程服务器的文件了。
```

需要注意的是：

```
当你配置的根目录为/时，可以查看整个服务器的目录
当你配置的根目录为/home/<USERNAME>时，只可以查看当前账户下目录
```

**一个理想的模板**

```
1. 在本地分别建立一个工程文件夹 /project 和一个数据文件夹 /database
2. 使用PyCharm建立映射
	根目录 /
	映射1 /project→/home/<USERNAME>/WorkSpace
	映射2 /database→/data/<USERNAME>/DATA
```



# Contribution

- 纪宇、朱振宇完成了服务器和客户端配置的实践和教程，PyTorch开发环境的配置，并记录了过程中遇到的问题；
- 纪宇完成了PyCharm远程操作的教程；
- 刘洋完成了服务器端tensorflow-gpu的安装教程

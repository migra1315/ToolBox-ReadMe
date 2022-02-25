## 客户端教程（windows为例）

### 客户端设置SSH

1. 设置→程序→搜索“添加可选功能”→添加功能→安装openssh

2. 打开终端输入，生成SSH公钥和私钥在路径C:\Users\dell\\.ssh

   ```
   ssh-keygen -t rsa -b 4096
   ```

4. 验证连通性

   ```
   ssh USERNAME@YOURSERVERIP
   exit
	```

### VSCode设置

1. 在左侧拓展处搜索remote -SSH，下载安装；搜索python，下载安装

2. 打开安装的“远程资源管理器”，选择SSH Target，点+号配置新连接 

   ```
   ssh USERNAME@YOURSERVERIP
   ```

   配置会以文件存储，一般选择默认的存储路径即可

3. 点齿轮，选择配置文件可以个性化修改

### 连接

远程资源管理器→SSH Target，点击/右键连接

如果没有把公钥上传服务器的话，需要输入用户密码；

如果已上传服务器，可直接建立连接；

## 服务器端教程

1. 参考客户端教程，先连接服务器公用账户，来配置自己的私有账户

   ```
   ssh user@YOURSERVERIP
   ```

2. 新建用户, USERNAME是你自定义的用户名

   ```
   sudo useradd -g user -G sudo -s /bin/bash -m USERNAME
   sudo passwd USERNAME
   ```

3. 切换用户

   ```
   su - USERNAME
   ```

4. 数据集和公共资源存储在机械硬盘

   ```
   mkdir /data/YOURFILENAME #新建文件夹
   sudo chmod 777 /data/YOURFILENAME #将你的文件夹权限改为全部可读可写
   ```

5. 更改权限

   **注意：**此处是修改你的用户home的读写权限，为了安全给大家设成了754，但这样在我们从客户端向服务器传数据的时候会稍微麻烦一点。嫌麻烦的同学请直接设成777

   ```
   sudo chmod 754 /home/USERNAME #用户rwx;组rx;其他组r
   ```

6. 生成SSH密钥

   ```
   ssh-keygen -t rsa
   cd /home/USERNAME/.ssh
   ls
   # 并将客户端公钥.pub拷贝至上文件夹，重命名为authorized_keys
   ```

7. 可选：拷贝路径下的公钥 id_rsa.pub 中内容，复制并修改文件名为authorized_keys到服务器~\\.ssh中。可以避免每次都要输密码

8. 配置conda环境变量

   ```
   echo 'export PATH="/home/anaconda3/bin:$PATH"' >> ~/.bashrc
   source ~/.bashrc
   conda init
   ```

9. 重启终端

10. 按需配置开发环境, ENVNAME是你自定义的虚拟环境名

   ```
   conda create -n ENVNAME python=3.8
   conda activate ENVNAME
   conda install pytorch torchvision torchaudio cudatoolkit=11.1 -c pytorch-lts -c nvidia
   ```

11. 此后可以在客户端连接我们自己的账户

    ````
    ssh USERNAME@YOURSERVERIP
    ````


## 客户端把文件复制到服务器（ubuntu）

1. 赋予当前客户端用户待复制的文件/文件夹读取权限

   ```
   # 注意分别在客户端和服务器端操作，读写权限已经设成777的可忽略本步骤
   sudo chmod 777 local_file_path #ubuntu客户端,windows可忽略
   sudo chmod 777 remote_file_path #服务器
   ```

2. 传输

   ```
   scp -r local_file_path USERNAME@202.194.201.10:remote_file_path #文件夹
   scp local_file_path USERNAME@202.194.201.10:remote_file_path #文件
   ```

3. 设置回原读写权限

   ```
   # 注意分别在客户端和服务器端操作
   sudo chmod 754 local_file_path #ubuntu客户端,windows可忽略
   sudo chmod 754 remote_file_path #服务器
   ```

## 遇到的问题

### Failed to initialize NVML: Driver/library version mismatch

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
sudo ./NVIDIA-Linux-x86_64-460.84.run -no-x-check -no-nouveau-check -no-opengl-files
sudo
## 禁用更新
sudo apt-mark hold nvidia-driver-460
```

## 机械硬盘自动挂载

```
df -h # 查看已挂载的硬盘，如果没有/dev/sda1, 也就是我们的机械盘（3.7TB）
sudo fdisk -l # 查看全部的硬盘
sudo mount /dev/sda /data #将机械盘挂载到/data下
sudo chmod 777 /data #解锁读写权限

```


## 切换lightdm图形界面（为了使用向日葵）

```
sudo apt install lightdm
sudo dpkg-reconfigure lightdm
```

## 注意事项

- 注意数据安全，所有的权限都写成 777 是绝对不安全的！及时备份， 及时改权限！
- 服务器属于实验室公共资源，**请勿将密码等信息外传**，导致中挖矿病毒或文件损失
- 重启电脑之后，请选择user用户登陆，我们的远程控制软件安装在user用户下




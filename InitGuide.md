

# PART Ⅱ



## 遇到的问题和规避的方法

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
sudo NVIDIA-Linux-x86_64-460.84.run -no-x-check -no-nouveau-check -no-opengl-files
sudo
## 禁用更新
sudo apt-mark hold nvidia-driver-460
```

### 禁用内核更新

```
apt-mark hold linux-base linux-image-generic linux-headers-generic libssl1.1 # 屏蔽内核自动更新
apt-mark unhold linux-base linux-image-generic linux-headers-generic libssl1.1 # 重启内核自动更新
systemctl disable {snapd,snapd.socket,snapd.refresh.timer} # 使用snap的情况下，禁用snap
systemctl stop {snapd,snapd.socket,snapd.refresh.timer} # 使用snap的情况下，禁用snap
# 禁用apt自动更新
vim /etc/apt/apt.conf.d/20auto-upgrades
# 将Update-Package-List等所有项目均写为0
# 禁用驱动自动更新
sudo apt-mark hold nvidia-driver-460
```

### 机械硬盘自动挂载

```
df -h # 查看已挂载的硬盘，如果没有/dev/sda1, 也就是我们的机械盘（3.7TB）
sudo fdisk -l # 查看全部的硬盘
sudo mount /dev/sda1 /data #将机械盘挂载到/data下
sudo chmod 777 /data #解锁读写权限
----
# 自动挂载
sudo blkid #查看硬盘的UUID
sudo vim /etc/fstab
加入
UUID=cb7539fe-9191-471d-ba26-44323059667b /data ext4 defaults 0 1
```


### 切换lightdm图形界面（向日葵远控只支持lightdm）

```
sudo apt install lightdm
sudo dpkg-reconfigure lightdm
```
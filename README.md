# 配置开发环境流程

### 一、安装ubuntu18.04系统

#### 1. 准备

使用rufus制作ubuntu18.04 安装盘

#### 2. 安装系统

插上U盘，USB启动，install ubuntu，中文，全选，其他选项，分区（重点）

手动分区，假设留出的空闲分区为 80G，点击空闲盘符，点击"+"进行分区，如下：

1. efi：大小500M，逻辑分区，空间起始位置，用于efi；

2. swap：8G，逻辑分区，空间起始位置，用于"swap"或"交换空间"。

3. /：  20G，主分区，空间起始位置，用于"ext4日志文件系统"，挂载点为"/"。

4. /home:剩下的全分给它，逻辑分区，空间起始位置，用于"ext4日志文件系统"，挂载点为"/home"。

下拉列表选择Ubuntu的efi分区编号，之后点击"Install Now"。

地区上海，默认英语键盘，姓名、计算机名、用户名、密码，安装，等待完成。

#### 3.  更换阿里源

```Terminal
sudo cp /etc/apt/sources.list sources.list.old   # 把以前的先保存一下
sudo vim /etc/apt/sources.list                   # 打开源文件配置进行换源，填写下面的阿里源
```

```Terminal
# ubuntu18.04阿里源
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
```

```Terminal
sudo apt-get update                              # 填写进后保存，后续更新一下，就可以了
```

设置——网络——勾选所有。

### 二、安装ssh-server

#### 1、安装openssh-server

1. 安装

```Terminal
sudo apt-get install openssh-server
```

2. 启动

```Terminal
sudo service ssh start
```

3. 查询服务启动状态：

```Terminal
sudo ps -e | grep ssh
```

#### 2、开机自启动ssh

```Terminal
sudo systemctl enable ssh
```

#### 3、设置好后重启系统

```Terminal
sudo reboot
```

#### 4、查看ssh是否启动

```Terminal
sudo systemctl status ssh
```

看到Active: active (running)即表示成功

### 三、安装cmake

#### 下载Binary版(下载即用)***(https://cmake.org/download/)***

下载后将文件提取(解压)出来，复制到/usr/local/bin/

```Terminal
sudo cp -r /home/up/下载/cmake-3.23.1/ /usr/local/bin/
```

创建软链接，终端输入：

```Terminal
sudo ln -sf /usr/local/bin/cmake-3.23.1/bin/* /usr/local/bin/
```

查看cmake版本，另起终端输入：

```Terminal
cmake --version
```

### 四、安装ROS

#### 1、设置sources.list

```Terminal
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

#### 2、设置key

```Terminal
sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
```

#### 3、更新package

```Terminal
sudo apt-get update
```

#### 4、安装ROS melodic完整版

```Terminal
sudo apt-get install ros-melodic-desktop-full  # 根据ubuntu版本更改
```

#### 5、初始化rosdep

```Terminal
sudo rosdep init
rosdep update
```

此处连接不上会报错，需要魔法。

#### 6、配置ROS环境

```Terminal
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc  # 根据ubuntu版本更改
source ~/.bashrc
```

#### 7、安装依赖项

```Terminal
sudo apt-get install python-rosinstall python-rosinstall-generator python-wstool build-essential
```

#### 8、测试ROS是否安装成功

1. 开启新终端输入以下命令，初始化ROS环境：

```Terminal
roscore
```

2. 再打开一个新的终端（Termial），输入以下命令，弹出一个小乌龟窗口：

```Terminal
rosrun turtlesim turtlesim_node
```

3. 出现一个小乌龟的LOGO后，再 打开一个新的终端（Termial），输入以下命令：

```Terminal
rosrun turtlesim turtle_teleop_key
```

4. 打开新的Termial，输入以下命令，可以查看ROS节点信息：

```Terminal
rosrun rqt_graph rqt_graph
```

顺利完成以上步骤则安装成功。

### 五、安装Onboard SDK和ROS版本

#### 1．注册大疆开发者账号

***(https://developer.dji.com/cn/)***
创建OboardSDK的app获取app_id和app_key。

#### 2．下载OnboardSDK包

下载地址：***[https://github.co/dji-sdk/](https://github.com/dji-sdk/Onboard-SDK/tree/3.8.1)[O](https://github.com/dji-sdk/Onboard-SDK/tree/3.8.1)[nboard-SDK/tree/3.8.1](https://github.com/dji-sdk/Onboard-SDK/tree/3.8.1)***

#### 3．编译djiosdk-core模块

在OnboardSDK文件夹内打开终端输入：

```Terminal
mkdir build
cd build
cmake ..
make djiosdk-core
sudo make install djiosdk-core
```

此时若想运行例程或进行OSDK开发：

```Terminal
cp ../sample/linux/common/UserConfig.txt bin/
cd bin
gedit UserConfig.txt
```

将前面申请的app_id和app_key填入，波特率为230400，串口确定无误后，连接Dji Assistant 2，遥控器开启并切换F模式，可运行例程进行仿真：

```Terminal
./djiosdk-flightcontrol-sample UserConfig.txt
```

其他例程也可以试一试。

#### 3．创建ROS工作空间并初始化

建立新终端，输入：

```Terminal
mkdir -p ~/catkin_ws/src
cd catkin_ws/src
catkin_init_workspace
```

#### 4．下载OnboardSDK-ROS包

下载地址：***https://github.com/dji-sdk/Onboard-SDK-ROS/tree/3.8.1***

把下载的OnboardSDK-ROS包解压到~/catkin_ws/src下，编译dji_sdk和dji_sdk_demo：

```Terminal
cd ~/catkin_ws
catkin_make
```

这里可能会失败，终端输入：

```Terminal
sudo apt-get install ros-melodic-nmea-msgs    #根据ubuntu版本更改
```

重新输入：

```Terminal
catkin_make
```

完成后：

```Terminal
cd src/dji_sdk/launch/sdk.launch
gedit sdk.launch
```

将前面申请的app_id和app_key写入launch文件，修改波特率为230400，后续运行时，需修改串口名为实际串口名。

最后，添加UART读写权限，启动终端，输入：

```Terminal
sudo usermod -a -G dialout $USER
```

重启计算机，或注销账户重新登陆（可能不行），即可获取读写权限。

#### 5．运行仿真例程

新开终端，输入

```Terminal
cd catkin_ws
source devel/setup.bash
roslaunch dji_sdk sdk.launch
```

若出现错误，依次检查串口名，TX和RX是否反接，USB-TTL模块是否松动等，还是不行就重启试试，出现successful即成功，连接Dji Assistant 2，遥控器开启并切换F模式，可运行仿真例程，新启终端，输入：

```Terminal
source devel/setup.bash
rosrun dji_sdk_demo demo_flight_control
```

其他例程也可以试一试。

### 六、MATLAB——ROS通讯

#### 1. 本地MATLAB通讯

MATLAB r2020b 以上版本安装ROS Toolbox

matlab r2020a 以下版本除安装ROS Toolbox外，还需安装ROS Toolbox interface for ROS Custom Messages

无论哪种版本，安装完成后在matlab命令行里输入

```matlab
folderpath=fullfile('/home/用户名/catkin_ws/src')
rosgenmsg(folderpath)
```

按照提示继续操作，

完成。

#### 2. 局域网MATLAB通讯

在局域网内其他计算机使用MATLAB与ROS通信，

### 附：MATLAB中关于ROS的说明

### ROS System Requirements

#### 1. ROS 1 Noetic Ninjemys Requirements

Since R2022a, the ROS Toolbox supports the [ROS Noetic Ninjemys](https://wiki.ros.org/noetic) distribution for ROS.

#### Platforms

- Windows® 10 only
- Linux® — Ubuntu 20.04 recommended
- macOS

For more information about ROS Noetic support, see their [Target Platforms](https://www.ros.org/reps/rep-0003.html#noetic-ninjemys-may-2020-may-2025) page.

#### ROS Node Deployment and Custom Messages

When generating custom messages for ROS, or deploying ROS nodes from MATLAB® or Simulink®, you must build the necessary ROS packages. This requires you to have Python®, CMake, and a C++ compiler for your platform.

**Python Version 3.9**

- To connect to ROS networks with the [`rosinit`](https://ww2.mathworks.cn/help/ros/ref/rosinit.html) function, you must install and set up [Python](https://www.python.org/downloads/) 3.9.

- To check your Python version, use the [`pyenv`](https://ww2.mathworks.cn/help/matlab/ref/pyenv.html) function.

  ```matlab
  pyenv
  ```

  ```matlab
  ans = 
    PythonEnvironment with properties:
  
            Version: "3.9"
         Executable: "C:\Python39\python.exe"
            Library: "C:\Python39\python39.dll"
               Home: "C:\Python39"
             Status: NotLoaded
      ExecutionMode: InProcess
  ```

- If your Python interpreter is set to a different version, restart MATLAB and set the version with [`pyenv`](https://ww2.mathworks.cn/help/matlab/ref/pyenv.html).

  ```matlab
  pyenv('Version','3.9')
  ```

**CMake**

- Download and install [CMake](https://cmake.org/download/) 3.16.3+.

**C++ Compilers**

- Windows — [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019
- Linux — [GNU Compiler Collection (GCC)](https://gcc.gnu.org/) 6.3+
- macOS — [Xcode](https://developer.apple.com/xcode/) 10+

### 2. ROS 1 Melodic Morenia Requirements

From R2020b to R2021b, the ROS Toolbox supports the [ROS Melodic Morenia](https://wiki.ros.org/melodic) distribution for ROS.

#### Platforms

- Windows — Windows 10 recommended for all ROS distributions. For [ROS Melodic](https://wiki.ros.org/melodic), only Windows 10 is supported.
- Linux — Ubuntu 18.04 recommended.
- Mac OS X

For more information about ROS Melodic support, see their [Target Platforms](https://www.ros.org/reps/rep-0003.html#melodic-morenia-may-2018-may-2023) page.

#### ROS Node Deployment and Custom Messages

When generating custom messages for ROS, or deploying ROS nodes from MATLAB or Simulink, you must build the necessary ROS packages. This requires you to have Python, CMake, and a C++ compiler for your platform:

**Python Version 2.7**

- To connect to ROS networks with the [`rosinit`](https://ww2.mathworks.cn/help/ros/ref/rosinit.html) function, you must install and set up [Python](https://www.python.org/downloads/) 2.7.

- To check your Python version, use the [`pyenv`](https://ww2.mathworks.cn/help/matlab/ref/pyenv.html) function.

  ```matlab
  pyenv
  ```

  ```matlab
  ans = 
    PythonEnvironment with properties:
  
            Version: "2.7"
         Executable: "C:\Python27\pythonw.exe"
            Library: "C:\windows\system32\python27.dll"
               Home: "C:\Python27"
             Status: NotLoaded
      ExecutionMode: OutOfProcess
  ```

- If your Python interpreter is set to a different version, restart MATLAB and set the version with [`pyenv`](https://ww2.mathworks.cn/help/matlab/ref/pyenv.html).

  ```matlab
  pyenv('Version','2.7')
  ```

**CMake**

- Download and install [CMake](https://cmake.org/download/) 3.15.5+.

**C++ Compilers**

- Windows — [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 or 2019
- Linux — [GNU Compiler Collection (GCC)](https://gcc.gnu.org/) 6.3+
- macOS — [Xcode](https://developer.apple.com/xcode/) 10+

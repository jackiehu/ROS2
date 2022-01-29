# ROS2学习笔记
## 第一周

新手准备上手ROS2研究下，之前在X86平台安装过一次，可以跑HelloWord，后来专门弄了个Mac mini M1芯片的，准备虚拟机安装下ARM平台的，以后好方便用树莓派自己练手。国内的网络环境想安装一下ROS2还真是困难，并且我选了一个相对困难的需要编译的。
### 虚拟机
首先我安装了一个虚拟机Parallels Desktop，直接用它提供的ARM版本的Ubuntu系统20.04LTS，这个最好选发行版，不然会遇到各种问题。
安装好Ubuntu之后就开始进来第一个坑了，那就是源的问题，官方源最全但是无奈连不上，或者实在是太慢，我用了一下华为源还有中科大源都无法在后期编译ROS2的时候完美。后来只有清华源才能搞定（忘了哪个帖子看的说华为源或阿里源快的）。
[清华源 ARM](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu-ports/)
执行代码：
```
echo "deb https://mirror.tuna.tsinghua.edu.cn/ubuntu-ports/ focal main restricted universe multiverse
deb https://mirror.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-updates main restricted universe multiverse
deb https://mirror.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-backports main restricted universe multiverse
deb https://mirror.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-security main restricted universe multiverse
deb https://mirror.tuna.tsinghua.edu.cn/ubuntu-ports/ focal-proposed main restricted universe multiverse">/etc/apt/sources.list
```
执行完之后更新一下
`sudo apt-get update`

### 替换Github Host
因为我选的是源码构建ROS2的方式，所以保持GitHub的链接稳定是需要先弄一下的。MAC上有一个 SwitchHosts 可以切换，
[我用的这个远程链接](https://github.com/521xueweihan/GitHub520)，实际上还需要添加raw.githubusercontent.com的DNS指向108到111。
搞定DNS后开始按部就班的照着[官方教程](https://docs.ros.org/en/galactic/Installation/Ubuntu-Development-Setup.html) 一步步地进行。

* 坑1
如果不进行以上的两步在官方教程的
```
python3 -m pip install -U importlib-metadata importlib-resources
```
这一步就会因为资源问题卡住报错。
* 坑2
虽然替换了GitHub的DNS，但是
```
wget https://raw.githubusercontent.com/ros2/ros2/galactic/ros2.repos

```
这一步还是会因为20K的网速败下阵来，消磨掉你的激情。
所以我很烦躁的把一个个的Github全都镜像到了码云上，然后批量修改了ros2.repos代码拉取地址到了我的镜像上，[镜像地址](https://gitee.com/jackiehu1122/projects)。你会体会到十几兆的Git拉取代码的速度，几分钟搞定。

* 坑3
Rosdep update阶段不知道为啥电信宽带就是连不上Github，后来想起了手机网络可以打开GitHub，然后我就开了中国移动的热点，顺利进到了下一步
* 坑4
当你到了编译阶段
```
colcon build --symlink-install
```
你会发现因为网络问题带来的坑让你怀疑人生，我都想直接去下载狐狸得了，为啥非要搞银河。还有就是编译过程中有些头文件找不到直接报错导致编译失败。

### 网上学科
经过了这四个大坑我对我家里的宽带产生了清晰的认知。看来只能网上学科了。
本来不想弄，因为我想树莓派上也没有啥可用的ARM版本的VPN啥的，看来以后得在路由上边动手脚了。
我修改了虚拟机的网络连接方式为桥接，然后改了Mac上的代理地址为0.0.0.0还有Ubuntu里的代理地址为Mac的IP地址后，这下虚拟机就可以用Mac上的网上学科了。顺利的开始编译，但还是会遇到三个问题。
1. zstd_vendor问题
2. share_queues_vendor问题
3. rviz_ogre_vendor问题
都是应为下载太慢都编译到了324然后停下来。
解决办法是自己手动下载，然后改名后复制到相应的文件目录下

* zstd_vendor：下载后zip包改名改成：v1.4.4.zip，复制到build/zstd_vendor/zstd-1.4.4-prefix/src里面
* share_queues_vendor：下载后zip包改名改成：8f65a8734d77c3cc00d74c0532efca872931d3ce.zip，复制到build/shared_queues_vendor/multiple里面
* rviz_ogre_vendor：下载后zip包改名改成：v1.12.1.zip，复制到build/rviz_ogre_vendor/ogre-v1.12.1-prefix/src里面

注意，以前可能下载了一部分，需要删掉同名文件再拷贝过去。

最后终于成功运行。爬坑完毕。

我的目标是用Swift尝试下看能不能用在ROS2上。

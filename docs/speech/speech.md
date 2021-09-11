##  语音处理模块



**环境：**

- Hardware: Raspberry PI 4B - 8G - SD 卡(16G)

- OS ：Raspberry Pi OS 2021Desktop [Download](https://www.raspberrypi.org/software/raspberry-pi-desktop/)
- Python3/ Win32DiskImager(烧录镜像软件) Or Raspberry Pi Imager(树莓派自带镜像烧录软件)
- USB 麦克风/音箱（耳机）

----

## 1. 搭建操作系统

**Step 1:** 官网下载镜像文件，经测试树莓派4B安装 Ubuntu20 桌面版本经常出现卡顿现象，而且 Ubuntu20 总是出现令人抓狂的依赖问题，最终还是刷回了 Raspberry  Pi OS 2021桌面版系统。官网镜像下载慢的话，可以使用迅雷下载，通常能在 5 分钟左右下载完成。



**Step 2:** 使用 [Win32DiskImager](https://win32diskimager.download/) 将下载好的镜像烧录进 SD 卡中，烧录 Ubuntu20 多次出现无法树莓派无法启动的现象。最稳定的还是 Raspberry Pi OS。



**Step 3:** 烧录好镜像之后，将 SD 卡插入树莓派，通电启动。如果出现树莓派标志和二维码则说明镜像烧录出现故障，需要重新烧录镜像。Ubuntu 18 以下的系统似乎很难成功烧录并运行，搜索了很久也没有找到正确的解决办法，如果有小伙伴成功了，不妨分享一下。



**Step 4:** 系统尽量设置成英文，避免莫名其妙的故障。成功启动树莓派之后，需要更换国内源。在此之前需要对 **vi 配置**进行修改，默认配置极其难用，上下左右键各种恶心，修改如下：

```bash
sudo vi /etc/vim/vimrc.tiny
```

```
# set compatible 
set nocompatible 

# 增加一行
set backspace=2
```

按 Esc 键，再输入 :wq! 

**tips:** 如果编辑过程中误按了上下左右键，出现字母又无法恢复的情况，按一下 Esc ，再按一下 字母 d 键进行删除。按字母 i 键进入编辑模式，重新输入即可。(注意 d 键是删除一整行，请注意)



**Step 5:** 完成以上步骤，就可以使用 vi 来修改源文件，增加国内源。在修改源文件之前，请先备份，指令如下：

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo cp /etc/apt/sources.list.d/raspi.list /etc/apt/sources.list.d/raspi.list.bak
```

备份好后，修改 sources.list 文件和 raspi.list 文件如下：

1. **sources.list**

```bash
sudo vi /etc/apt/sources.list
```

注释掉之前的内容，最后添加两句:

```bash
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ buster main contrib non-free rpi
```

2. **raspi.list** 

```bash
sudo vi /etc/apt/sources.list.d/raspi.list
```

注释掉之前的内容，最后添加两句：

```bash
deb http://mirror.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui 
deb-src http://mirror.tuna.tsinghua.edu.cn/raspberrypi/ buster main ui
```

**tips:** 虽然有很多国内源，但是此项目使用清华源出现的坑最少。

```bash
sudo apt-get update # 更新系统软件 
```

**如果不习惯使用单调无趣的 vi** ，可以安装功能更加强大的 vim：

```bash
sudo apt-get update 
sudo apt-get install vim
```

 **如果vim右键赋值出了问题**，树莓派默认装好vim之后，右键不能粘贴，反而进入了visual模式，那么执行以下操作

```bash
sudo vim /usr/share/vim/vim80/defaults.vim
```

在第 70 行， mouse=a 中间加 '-', 改为: mouse-=a

  ![img](https://img-blog.csdnimg.cn/20190424113110149.png)

 

**如果在右键粘贴大批数据的时候乱序**，你需要进行以下操作，树莓派4变成了vim81

```bash
sudo vim /usr/share/vim/vim80/defaults.vim
```

  在最后面加上一句，这样粘贴的时候，按一下F11，然后你就粘贴不乱序了，再按一下F11退出粘贴数据模式。  

```bash
set pastetoggle=<F11>
```

**更改树莓派名称**（**记住，这是你的树莓派名称，不是你的登陆账号**）以及登陆密码

  1、在hosts和hostname里面都需要更改树莓派的名称，博主的是littlePi，是不是很萌

```bash
sudo vi /etc/hosts
sudo vi /etc/hostname
```

  在hosts里面更改这个后面的localhost为你自己的树莓派名称

![img](https://img-blog.csdnimg.cn/20190424115029949.png)

  在hostname里面，也个更改为自己的名称

![img](https://img-blog.csdnimg.cn/20190424115125317.png)

  2、接下来就是更改密码了，依次更改***\*账号pi和root\****的密码，这里账号已经默认有pi和root了

```bash
sudo passwd pi
sudo passwd root
```

  依次输入密码并且确认密码即可.

------------



## 2. 搭建 wukong-robot 运行环境



### Python2的方案如下：

### 首先卸载树莓本身的pyaudio软件，然后进行安装

```bash
sudo apt remove python-pyaudio python3-pyaudio
sudo apt install libasound-dev
sudo apt-get install libatlas-base-dev sox swig
```

接下来是安装pyaudio，这里需要先进行下载，到如下[网址下载](http://www.portaudio.com/download.html) pa_stable_v190600_20161030.tgz

```
http://www.portaudio.com/download.html
```

 ![img](https://img-blog.csdnimg.cn/20200423103122586.png)

对 pa_stable_v190600_20161030.tgz 进行解压 通过 tar -xzf pa_stable_v190600_20161030.tgz 进行解压，然后进行安装

进到解压后的文件下面，进行如下操作

```bash
sudo ./configure
sudo make
sudo make install
sudo apt-get install python-pyaudio python3-pyaudio
```

这样就安装好了

 

### **Python3的方案如下：**

**先按照Python2的方式把所有的都装一遍**

安装 PyAudio：

```bash
sudo apt-get install python3-pyaudio
```

安装 SWIG ：

```bash
sudo apt-get install swig
```

安装 ATLAS：

```bash
sudo apt-get install libatlas-base-dev
```

安装pluseaudio和python3-dev，不然会报出9997的问题

```bash
sudo apt-get install -y pulseaudio python3-dev
```

下载snowboy源码

```bash
git clone git@gitee.com:harrytsz/snowboy.git
```

进到

```bash
cd snowboy/swig/Python3
```

然后

```bash
sudo make
```

进到Python3的的例子中

```bash
cd  snowboy/examples/Python3
```

开始运行，喊一声snowboy就可以听到叮的一声

```bash
python3 demo.py resources/models/snowboy.umdl
```

会出现报错

把官方案例文件中的 snowboydecoder.py 文件修改一下，把from . import snowboydetect改为import snowboydetect然后再运行。就ok了。

参考链接

```
https://www.pianshen.com/article/7751946046/
```

 

### **3、 树莓派如果新增了麦克风的输入设备，需要进行如下操作，改配置文件让usb声卡作为默认的音频输入**

```bash
#在/home/pi目录下新增如下文件
sudo vim .asoundrc

#新增如下内容 (这个地方根据自己的来，默认按照如下情况，特殊情况特殊对待)
pcm.!default {
  type asym
   playback.pcm {
     type plug
     slave.pcm "hw:0,0"
   }

   capture.pcm {
     type plug
     slave.pcm "hw:1,0"
   }
}
```

 

以上步骤安装完成 snowboy 模块，接下来就是使用 snowboy 唤醒模块来配合百度语音识别和合成 API 以及智能对话机器人 API 完成语音到文字再传输到智能对话系统中，接收到智能对话系统的反馈之后，再将文字转换为语音信息。

-------

### 手动安装 wukong-robot 

[wukong-robot 官网](https://wukong.hahack.com/#/)

1. 克隆本仓库：

```bash
git clone https://github.com/wzpan/wukong-robot.gitCopy to clipboardErrorCopied
```

2. 安装 sox ，ffmpeg 和 PyAudio：

Linux 系统

```bash
sudo apt-get install portaudio19-dev python-pyaudio python3-pyaudio sox pulseaudio libsox-fmt-all ffmpeg
pip3 install pyaudioCopy to clipboardErrorCopied
```

如果遇到 pip3 安装慢的问题，可以考虑使用 Pypi 镜像。例如 [清华大学 Pypi 镜像](https://mirror.tuna.tsinghua.edu.cn/help/pypi/) 。

Mac 系统：

```bash
brew install portaudio sox ffmpeg
pip3 install pyaudioCopy to clipboardErrorCopied
```

如果你没有 Homebrew ，参考[本文](http://brew.sh/)安装

3. ## **安装依赖的库：**

   这一步极其重要，因为这个项目年代久远，操作系统环境依赖已经没有办法解决，经过多次尝试。我选择将 requirements.txt 中的版本号全部删除，python 库全部选择默认安装最新版本，最终成功解决了令人吐血的无限循环依赖问题，执行如下命令:

```bash
cd wukong-robot
pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple/ -r requirements.txt
```

requirements.txt 安装完成之后，就可以进行最后一步。命令行进入 wukong-robot/ 目录下，执行:

```bash
python3 wukong.py
```

弹出提示，输入 'y' 即可。

此时，不出意外就能听到提示音。对着麦克风大喊“孙悟空”，如果能得到智能的回应，则说明整个链路全部跑通了，接下来就是功能扩展部分了。如果一直得到无法听清你在说什么的提示音的话，就需要去检查图灵机器人的 API 是否已经失效，换个图灵机器人 API 重新测试即可。如果无法听到语音播报，则有可能是百度语音识别模块出现问题，需要检查百度语音识别 API 是否失效，注册百度语音服务账号，然后将自己的 AppId 等信息填入到后台管理选项中。

在浏览器中输入 localhost:5000 ，即可进入登陆页面，账号默认 wukong ，密码默认：wukong@2019

登录进后台之后，就可以对 wukong-robot 的 API 进行修改和配置了。



## 3. 测试智能语音 API

百度语音识别接口：https://console.bce.baidu.com/ai/?fromai=1#/ai/speech/overview/index

参考博客：https://www.cnblogs.com/apollo1616/p/10274511.html

人工智能(百度AI+图灵) https://www.cnblogs.com/lbzbky/articles/12088900.html



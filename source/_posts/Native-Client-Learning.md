---
title: Native Client Learning
date: 2016-11-07 10:51:31
tags:
  - Native Client
  - NaCl
comments: true
updated:
categories: 技术
---

## Technical Overview（技术概要）
1. Native Client
  {% asset_img web-app-with-nacl.png  A web application with and without Native Client %}
  Native Client  分为传统的Nacl （NaCl）和移动的NaCl（PNaCl）,传统的必须通过Chrome Web Store 进行下载，而PNaCl 只需要打开浏览器就能够进行加载运行了
<!--more-->
2. How Native Client works （Native Client 是如何进行工作的）
  Native Client 作为开发C/C++ 应用程序在浏览器上安全运行的软件组件的名称，Native Client 包括如下：
  2.1 Toolchains（工具链）：移植C/C++ 代码到Portable of Native Client modules 或者 Native Client modules的开发工具集合（编译、链接等）。
  2.2 Runtime components（运行时组件）：能够安全高效的执行Native Client modules 的浏览器插件或者是其宿主平台
     下面这张图展示这些组件的交互
     {% asset_img nacl-pnacl-component-diagram.png  The Native Client toolchains and their outputs %}
  2.3 Toolchains
      Native Client toolchains 包括一个编译器、链接器、汇编器 和其工具来将C/C++ 源码转化为可以在浏览器中加载的模块
      Native Client SDK 提供两个工具链
     2.3.1. Portable Native Client（PNaCl，pronounced“pinnacle”）。pexe
        * 将源代码编译成二进制可执行文件pexe
        * 在加载进浏览器时，会将pexe 转为host-specific，也可以说是nexe
     2.3.2.（non-portable）Native Client。 nexe
        PNaCl toolchains 适合大多数应用，NaCl-GCC toolchains 只被用作不在公开网络上分发的应用。
3. Structure of a web application（Web 应用结构）
  * HTML and CSS： HTML文件告诉浏览器如何去这个manifest（nmf file）（清单文件）通过embed tag
```cmd
    <embed name = 'mygame' src='mygame.nmf' type = 'application/x-pnacl'/>
```
  * Manifest： 指示模块去加载和配置选项
```cmd
     {
          ...
          ...
          'url':'mygame.pexe'
     }
```
  * pexe（portable NaCl file）：一个编译过的Native Client 模块。使用pepper API 来提供JavaScript 和其他浏览器资源交互
  {% asset_img nacl-in-a-web-app.png  Structure of a web application %}
  * Pepper plugin-in API
    The Pepper plug-in API(PPAPI)，是一个做为一个开源的、跨平台C/C++ API 的web 浏览器插件程序。可以安全便捷的访问系统级别功能。包括
    1. 同应用中的JavaScript 交互
    2. 操作文件读写
    3. 播放音频
    4. 渲染3D 图像

## 配置环境
1. Python 2.7
  首先需要在本地安装Python，需要安装2.7.x, 现在还不支持3.x ，同时需要将Python 目录添加到path 环境目录中
  1.1.  https://www.python.org/download/  下载最新的2.x Python ,资源目录1
  1.2.  需要重启之后才生效
2. Make
  在Windows 系统上，Native Client SDK包含了一个GNU Make
3. Platforms
  Native Client 支持多个操作系统，包括Windows，Linux，OSX，和ChromeOS，也支持多个架构包括x86-32，x86-64，ARM 和MIPS
  本机是windows7 ,x86-64 架构
4. Versions
  只要是最新的Chrome 就可以了，同时需要注意Chrome 的版本要大于下载的pepper_<version>
  查看Chrome 版本，在浏览器地址栏中输入
  ```cmd
  about:chrome
  ```
  或者
  ```cmd
  about:version
  ```
5. Installing the SDK（The Native Client Software Development Kit）
  5.1. nacl_sdk.zip 下载安装，资源目录2
  5.2. 将下载下来的nacl_sdk.zip 进行解压，获取如下文件目录
```cmd
 nacl_sdk
  naclsdk（naclsdk.bat 是为Windows 系统适配的）: 更新工具，用来进行下载和更新bundles
  sdk_cache: 这个文件夹中包好一个manifest 文件，其列出了你已经下载的bundles
  sdk_tools: 由naclsdk 命令代码运行
```
  5.3. 安装稳定的bundle
    5.3.1. 首先是进入nacl_sdk 这个目录并运行naclsdk list 这个命令本地
```cmd
D:\workspace\samsung-Project\Tizen\NACL\nacl_sdk>naclsdk list
ERROR:Unable to read remote manifest from URL "https://storage.googleapis.com/na
tiveclient-mirror/nacl/nacl_sdk/naclsdk_manifest2.json".
<urlopen error [Errno 10060] >
```
  这个由于是在墙外导致的问题产生，不过将https://storage.googleapis.com/nativeclient-mirror/nacl/nacl_sdk/naclsdk_manifest2.json 这个文件下载了来，资源目录3
    5.3.2. 通过运行naclsdk update 来更新需要的bundles
    5.3.3. 通过运行 naclsdk list 来显示所有可用的bundles ，同时检测你已经安装的bundles
    5.3.4. 通过运行 naclsdk help 来获取帮助
  5.4. 到目前位置，由于GFW 问题，不能够通过naclsdk update 来安装bundle，那么接下来就是需要通过其他的方式来进行安装测试
  5.5. 将pepper_43.rar ，资源目录4 解压，放到nacl_sdk 这个目录下

## Build the SDK example（构建SDK 例子）
1. Makefile 脚本可以构建通过配置release 和debug 参数使用三种SDK 工具链（newlib，glibc，和PNaCl）可以构建不同的例子在每一个例子中，可以通过查看在Makefile 文件中的VALID_TOOLCHAINS 变量的一个值,在<code>pepper_43/example/api/core</code>中的Makefile 文件
```makefile
  VALID_TOOLCHAINS := pnacl newlib glibc clang-newlib win
```
  ，就是当你进行构建时默认的构建方法
```cmd
D:\workspace\samsung-Project\Tizen\NACL\nacl_sdk>cd pepper_43/examples/api/core
D:\workspace\samsung-Project\Tizen\NACL\nacl_sdk\pepper_43\examples\api\core>make
CXX  pnacl/Release/core.o
LINK pnacl/Release/core_unstripped.bc
FINALIZE pnacl/Release/core_unstripped.pexe
STRIP pnacl/Release/core.pexe
CREATE_NMF pnacl/Release/core.nmf
```cmd
  在<code>pepper_43/examples/api/core/pnacle/Release</code>目录中有许多pexe 文件，还可以通过配置命令来运行不同的方式
```cmd
  make TOOLCHAIN=newlib CONFIG=Release
```
  通过运行不同的make，可以如下结果
    * newlib 下有Debug 和Release 子目录
    * glibc 下有Debug 和Release 子目录
    * pnacl 下有Debug 和Release 子目录

## Run the SDK examples（运行SDK 例子）
1. Disable the Chrome cache （禁用Chrome 缓存）这是为了加载最新版本，同时许愿哦开启Developer Tools
   * F12 开启Developer tools
   * 点击右下角的gear icon
   * 在“General”设置下，续租“Disable cache”
2. Run the examples （运行例子）
```cmd
D:\workspace\samsung-Project\Tizen\NACL\nacl_sdk>cd pepper_43/examples/api/core
D:\workspace\samsung-Project\Tizen\NACL\nacl_sdk\pepper_43\examples\api\core>make run
```cmd
   需要注意的是，需要设置Chrome 的环境变量CHROME_PATH
3. Run the SDK examples as Chrome apps
   可以使用
```cmd
   make run_package
```
   来产生一个.crx 后缀的包，使用Chrome apps 来运行
4. Debugging the SDK examples（调试SDK 例子）
```cmd
   make debug
```  

## Simple PNaCl Web App（简单的PNaCl Web 应用）
1. 介绍了如何构建并运行一个PNaCl web 应用。这个客户端应用使用HTML、JavaScript 以及C++ 写的Native Client Module。PNaCl 工具链使这个Native Client module 直接在网页上运行
2. 在一个Web 页面如何加载Native Client module ，以及如何在JavaScript 和Native Client Module 之间进行通信
  2.1. 第一步: 下载并安装Native Client SDK
  2.2. 第二步：启动一个本地服务
  ```cmd
     cd pepper_$(VERSION)/getting_started
     make serve
  ```
    默认的端口号是5103，一个本地服务只是为了开发，而SDK 中的只是比较方便并不是必须的
  2.3. 第三步：设置Chrome 浏览器
     浏览器的版本至少和Natvie Client modules 的bundle 版本相同
      * F12 开启Developer tools ，不关闭
      * 点击右下角的gear icon
      * 在“General”设置下，选中“Disable cache”
  2.4. 第四步：教程实例代码
      * index.html  HTML 页面布局以及同Native Client Module 进行通信的JavaScript 代码Native Client Module 是一个在<embed> 标签中指向manifest file
      * hello_tutorial.nmf 一个manifest file，
      * hello_tutorail.cc C++ 代码的Native Client module
      * Makefile 编译命令将hello_tutorail.cc C++ 代码编译为pexe（portable executable）
  2.5. 第五步：编译Native Client module 并且运行这个应用
```cmd
          cd pepper_$(VERSION)/getting_started/part1
          make
```
    需要设置NACL_SDK_ROOT 环境变量，是为了不需要在nacl_sdk 目录下进行编译。
```cmd
          NACL_SDK_ROOTpa
          D:\workspace\samsung-Project\Tizen\NACL\nacl_sdk\pepper_43     
```
  2.6. 第六步：修改JavaScript 代码发送一条消息给Native Client module
      在index.html 中的moduleDidLoad 方法中，最后调用一个方法
```cmd
          HelloTutorialModule.postMessage('hello');
```
  2.7. 第七步：在Native Client module 中实现一个消息处理handler
    需要修改Native Client module (hello_tutorail.cc) 去相应来自JavaScript 代码的消息。
    2.7.1.  在module instance 中实现HandleMessage() 方法
    2.7.2. 调用PostMessage() 成员方法给JavaScript 发消息
  2.8. 第八步：编译Native Client module 并再次运行应用
    2.8.1. 编译Native Client module，
```cmd
          make
```
    2.8.2. 启动SDK web 服务
```cmd
          make serve
```          
    2.8.3. 在Chrome 浏览器中重新加载
```cmd
          http://localhost:5130/part1
```

## 参考文档
1. [Native Client 官网](https://developer.chrome.com/native-client/overview)
2. [从Native到Web，NaCl学习笔记（一）](http://www.sjsjw.com/kf_code/article/106_15218_13789.asp)

## 资源目录
1. 资源目录1
  链接：http://pan.baidu.com/s/1hqCSZHE 密码：jmgh
2. 资源目录2
  链接：http://pan.baidu.com/s/1dDHIOOL 密码：h4yw
3. 资源目录3
  链接：http://pan.baidu.com/s/1eQ8Trvc 密码：sy0z
4. 资源目录4
  链接: http://pan.baidu.com/s/1kT0BGUF 密码: qh3e

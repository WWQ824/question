# App包大小优化

## 背景
包大小一直是推广App的一个瓶颈，很多用户可能为了省点空间很轻易的将App卸载，所以占有较小空间的App很容易获客和留存。
 
## iOS包的构成
在 iPhone 上的应用包称为 ipa，即 iPhone Application。将 ipa 包解压后主要由以下四部分构成:
>1. 同名的可执行文件；
>2. Frameworks目录下的动态库
>3. Asset.car、.nib、.bundle、Localizable.strings 等资.源文件；
>4. 其他：_CodeSignature 文件夹，签名信息。

在iOS（和Mac OS X）上，主要的可执行文件格式是 Mach-O 格式。在 Windows 上的可直接执行的文件扩展名是 .exe，而在Linux（以及很多版本的Unix）系统上 ELF 是可直接执行的文件格式。
 
## Mach-O 简介
网上找了下有关Mach-O的介绍，如下：
Mach 内核被 NeXT 公司的 NeXTSTEP 操作系统使用。在 Mach 上，一种可执行的文件格是就是 Mach-O（Mach Object file format）。1996 年，乔布斯将 NeXTSTEP 带回苹果，成为了 OS X 的内核基础。所以虽然 Mac OS X 是 Unix 的“后代”，但所主要支持的可执行文件格式是Mach-O。
iOS 是从 OS X 演变而来，所以同样是支持 Mach-O 格式的可执行文件。
 
## Mach-O 的组成

### Mach-O 文件由三部分组成：
>1. 头部 (Header): Mach-O 文件的架构 比如 Mac 的 PPC, PPC64, IA-32, x86-64，iOS 的 arm系列。
>2. 加载命令(Load commands): 在虚拟内存中指定文件的逻辑结构和布局。
>3. 原始段数据（Raw segment data）:可以拥有多个段（segment），每个段可以拥有零个或多个区域（section）。每一个段（segment）都拥有一段虚拟地址映射到进程的地址空间。

 
所以优化的目标一个是资源一个就是Mach-O文件。 

# 优化方案

优化包大小无非就3类手段：删、压、移

1. 去除无用的资源、类文件、方法等，去除debug符号
2. 资源压缩、开启编译优化、混淆优化方法名
3. 资源服务化，代码插件化

# 官方 App Thinning
App Thinning 是由苹果公司推出的一项可以改善 App 下载进程的新技术，主要为了解决用户下载 App 耗费过高流量的问题，同时还可以节省用户 iOS 设备的存储空间。

App Thinning 会专门针对不同的设备来选择只适用于当前设备的内容以供下载。比如，iPhone 6 只会下载 @2x 分辨率的图片资源，iPhone 6Plus 则只会下载 @3x 分辨率的图片资源。

在苹果公司使用 App Thinning 之前，每个App都包含多个芯片的指令集架构文件。使用 App Thinning 之后，用户下载时就只会下载一个适合自己设备的芯片指令集架构文件。

App Thinning 包含三种方式，包括：App Slicing、Bitcode、On-Demand Resources。

* App Slicing 会在你想 iTunes Connect 上传 App 后，对 App 做切割，创建不同的变体，这样就可以适用到不同的设备
* On-Demand Resources 主要视为游戏多关卡场景服务的，它会根据用户的关卡金服下载随后几个关卡的资源，并且已经过关的资源也会被删掉，这样就可以减少初装 App 的包的大小。
* Bitcode 是针对特定设备进行包大小优化，主要目的是生成中间包便于后续架构扩展，优化不明显
其实，这里的大部分工作都是由 Xcode 和 App Store 来帮你完成的，你只需要通过 Xcode 添加 scassets 目录，然后将图片添加进来即可。所添加的 @2x 分辨率的图片和 @3x 分辨率的图片会在上传到 App Store 后被创建成不同的变体以减小 App 安装包的大小。而芯片指令集架构文件只需要按照默认的设置，App Store 就会根据设备创建不同的变体，每个变体里只有当前设备需要的那个芯片指令集架构文件。


1. 删
   * 可以借助LSUnusedResource或AppCode或fui等工具删除无用的资源、类文件、方法，
   * 合并重复的功能、组件，沉淀到基础组件。
   * 下线无用的业务和第三方SDK
   * 图片裁剪细分，要求相似的图片裁剪提供
   * 尽量少用图片，可使用代码绘制
   * 可借助第三方工具 [LinkMap](https://github.com/huanxsd/LinkMap) 解析Xcode生成的LinkMap文件, 分析代码文件大小
 
2. 压
   * 协调UI压缩资源
   * gif json化，使用lottie-ios来实现序列动画特效。
   * 混淆优化方法名暂无实现，感觉付出太大收获甚微。
   * 优化cocoapods对bundle的处理，修复其多份相同资源共存的bug。
   * 通过脚本将所有target的 ASSETCATALOG_COMPILER_OPTIMIZATION 设置为 space，丢掉一些性能。
   * 使用Assets.xcassets来管理图片，也可以有效压缩图片
   * 修改配置编译选项 
     * Generate Debug Symbols  Release下 设置为NO 
     * GCC_OPTIMIZATION_LEVEL Release下设置为Fastest，Smallest
     * Strip Debug Symbols During Copy 和 Symbols Hidden by Default 在release版本应该设为yes，可以去除不必要的调试符号
     * Strip Linked Product：设为YES，
     * Deployment Postprocessing：DEBUG下设为NO，RELEASE下设为YES，
 
3. 移
   * 更多的使用网络图片。
   * 动态化改造，尽量使用WebView和小程序实现


[有关project配置的文章](https://www.jianshu.com/p/11710e7ab661)  

一个普通的app来说可以这样配置这些设置：

* Generate Debug Symbols：DEBUG和RELEASE下均设为YES（和Xcode默认一致）；
* Debug Information Level：DEBUG和RELEASE下均设为Compiler default（和Xcode默认一致）；
* Deployment Postprocessing：DEBUG下设为NO，RELEASE下设为YES，这样RELEASE模式下就可以去除符号缩减app的大小（但是似乎设置为YES后，会牵涉一些和bitcode有关的设置，对于bitcode暂时还不太了解(´･_･`)）；
* Strip Linked Product：DEBUG下设为NO，RELEASE下设为YES，用于RELEASE模式下缩减app的大小；
* Strip Style：DEBUG和RELEASE下均设为All Symbols（和Xcode默认一致）；
* Strip Debug Symbols During Copy：DEBUG下设为NO，RELEASE下设为YES；
* Debug Information Format：DEBUG下设为DWARF，RELEASE下设为DWARF with dSYM File，dSYM文件需要用于符号化crash log（和Xcode默认一致）；
撰写工具分析各版本的linkmap和bundle大小，以及版本之间的差值。

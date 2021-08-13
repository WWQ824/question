# App启动优化

## App的启动可以分为2种
* 冷启动(Cold Launch):从零开始启动App
* 热启动(warm Launch):App已经在内存中, 在后台存活着, 再次点击图标启动App

## App启动时间的优化
* 主要是针对冷启动进行优化
* 通过添加环境变量可以打印出App的启动时间分析(Edit scheme -> Run -> Arguments)
  * `DYLD_PRINT_STATISTICS`设置为1
  * 更详细的信息 `DYLD_PRINT_STATISTICS_DETAILS`设置为1

## App冷启动可以概括为三大阶段
![](https://github.com/WWQ824/question/blob/main/%E5%90%AF%E5%8A%A8%E6%97%B6%E9%97%B4%E5%88%86%E6%9E%90%E5%9B%BE.png)

* dyld(dynamic link editor), Apple的动态链接器, 可以用来装载Mach-O文件(可执行文件, 动态库)
  * 装载App的可执行文件, 同时会递归加载所有依赖的动态库
  * 当dyld把可执行文件, 动态库都装载完毕后, 会通知Runtime进行下一步处理
* runtime
  * 调用`map_images`进行可执行文件内容的解析和处理
  * 在`load_images`中调用`call_load_medthods`, 调用所有Class和Category的`+load`方法
  * 进行各种Objc结构的初始化(注册Objc类, 初始化类对象...)
  * 调用C++静态初始化器和`_attribute_((constructor))`修饰的函数
  * 到此为止,可执行文件和动态库中所有的符号(Class, Protocol, Selector, IMP, ...)都已经按格式成功加载到内存中, 被runtime所管理
* main
  * App的启动由dyld主导, 将可执行文件加载到内存, 顺便加载所有依赖的动态库
  * 并由runtime负责加载成Objc定义的结构
 
## 不同阶段优化
* dlyd
  * 减少动态库, 合并一些动态库(定期清理不必要的动态库)
  * 减少Objc类, 分类的数量, 减少Selector数量(定期清理不必要的类和分类)
  * 减少C++虚函数数量
  * Swift尽量使用struct
* runtime
  * 用`+initialize`方法和`dispatch_once`取代所有的`_attribute_((constructor))`, C++静态构造器, ObjC的`+load`
* main
  * 在不影响用户体验的前提下, 尽可能将一些操作延迟, 不要全部都放在finishLaunching方法中

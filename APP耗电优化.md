# App耗电优化

## 耗电的主要来源
* CPU处理, Processing
* 网络, Networking
* 定位, Location
* 图像, Graphics

## 耗电优化
* 尽可能降低CPU, GPU功耗
* 少用定时器
* 优化I/O操作
  * 尽量不要频繁写入小数据,最好一次性写入
  * 读写大量重要数据时, 考虑dispatch_io, 其提供了基于GCD的异步操作文件I/O的API, 用dispatch_io系统会优化磁盘访问
  * 数据量比较大的, 建议使用数据库(比如SQLite, CoreData)
* 网络优化
  * 减少, 压缩网络数据(XML体积大, Json体积小, 上传文件要压缩)
  * 多次请求的结果是相同的, 尽量使用缓存(使用NSCache)
  * 使用断点续传,否则网络不稳定是可能多次传输相同的内容
  * 网络不可用时, 不要尝试执行网络请求
  * 让用户可以取消长时间运行或者速度很慢的网络操作, 设置合适的超时时间
  * 批量传输, 比如下载视频流时, 不要传输很小的数据包, 直接下载整个文件或者一大块一大块地下载; 如果下载广告, 一次性多下载一些, 然后慢慢展示. 如果下载电子邮件, 一次下载多封, 不要一封一封地下载
* 定位优化
    * 如果只是需要快速确定用户位置, 最好用`CLLocationManager`的`requestLocation`方法. 定位完成后, 会自动让定位硬件断电.
    * 如果不是导航应用, 尽量不要实时更新位置, 定位完毕就关掉定位服务
    * 尽量降低定位精度, 比如尽量不要使用精度最高的`kCLLocationAccuracyBest`
    * 需要后台定位时, 尽量设置`pauseLocationUpdatesAutomatically`为YES, 如果用户不太可能移动的时候系统会自动暂停位置更新
    * 尽量不要使用`startMonitoringSignificantLocationChanges`

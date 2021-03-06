# 通过嵌入式Linux + QT5.7 实现无线传感器网络的网关设计

## 2017.04.20

* 完成温湿度收集并显示到ARM开发板
* 实现了数据库操作的API封装
* 给CC2530引脚初始化会造成诡异现象，导致连续发送数据，网络瘫痪，避免随意初始化引脚，尤其是带CC2591的模块
* QCharts模块的坐标轴不会自动更新，所以画图的时候需要固定坐标轴，而且会连带之前的点进行画图

* 还有如下问题需要解决
  * 还需要解决COMBOX更新问题，主要是数据库更新一天的数据的时候要仔细处理
  * 增加网络通信，利用TCP套接字和上位机进行通信
  * 还需要增加登录密码的功能
 
BTW：希望明天能把上述三个问题解决好，下礼拜开始写论文。。。。。好怕找不到工作啊


* 对于ZigBee的应答机制部分，代码有待优化


## 2017.04.19

这几天是真的忙死啦，连做笔记的时间都没有。。。。。。
* 今天加了选择日期的功能，可以只显示指定日期的数据，提高读取数据库响应速度，只有日期为当天的时候才实时更新，不是当天就只显示静态曲线和数据
* 对于QT的MVC模型更准确的说是MVD模型，毕竟他把视图和控制合二为一以后，重在委托的实现上，通过委托，可以实现试图的自定义，并对模型进行读写数据
* 对于委托的的重载，要明确目前QT不支持自定义信号，可以通过它默认的commit以及close信号，将委托发生改变的数据记录下来，直接发送给试图或者其他对象
* 关键在于委托的函数都是const属性的，不支持在里面调用自定义的成员对象或者函数，避免修改const对象的成员，这个原因有待深入分析

* 还有以下几点需要解决
  * 数据库更新的问题--解决主键冲突
  * 封装自定义模型和试图类，使得具有可重用性，主要是在设置表头以及设置模型的数据类型，大致思路是通过设置public函数进行修改
  * 封装数据操作的函数，这些函数都隶属于MyThread类，统一一下
  * 初始化显示
  * 更新一天的数据库
  * 更新视图需要的显示，需要传递参数进行判断
  * 记录每一次采集的数据


## 2017.04.15
项目到今天为止开始了一个礼拜，现在才开始记录其中的笔记，以供以后复习。到目前为止完成的任务有如下几个：

* 实现开机登录界面的绘制
  * 使用QSS语法绘制按钮，实现按钮扁平化，需要加强CSS/QSS语法的学习
  * 布局使用Designer，这个不科学，后面的布局都是基于代码，Designer实际上效率上并不高
  * 绑定多个按钮控件到同一个槽函数，通过类型转换，实现对不同按钮的区分
  * 利用QString记录待处理的字符串，作为登录密码使用
  * 目前为了调试方便，不进行密码验证
  
  
* 实现图片浏览 
  * 该项目的一个重要功能是通过串口接收来自ZigBee协调器的数据，其中就有图片数据
  * 利用QT5自带的串口模块进行串口操作
  * 自定义通信协议，以方便解析数据来自什么类型、哪一号节点，有多长的有效数据
  * 每一个数据包分两次读取，一次读取包头，一次读取有效数据，由于QT串口的事件机制，使得串口并不会按照我们想法一次性读取完所有数据才返回，
  他会将一次数据分多次读取，多次返回，造成粘包现象，很难分析数据包，而且ZigBee协调器将会接收多种类型节点的数据，所以做好数据的解析极为重要，
  必须由我们人为制定通信协议，每次都指定要读取的长度，然后指导读取到目标长度的数据为止。
  
  
* 实现图片过滤筛选功能
   * 硬盘上将会存储大量的图片，到时候浏览及其不方便，实现一个检索图片的方法是必要的
   * 图片通过接收的时间来命名，所以对当前路径的图片名进行正则表达式处理可实现快速过滤
   * 利用QT的模型视图实现了图片文件的浏览功能
   * 这个地方由于嵌入式Linux不带键盘，所以自己实现一个建议的虚拟键盘，实现过滤条件的输入，为了实现键盘只有在LienEdit有效的时候才显示，
  对LineE进行重载，重写了鼠标按下事件，并在其他控件的槽函数里实现了对虚拟键盘的关闭功能
  
* 实现了温湿度的显示
   * 湿度用仪表盘，温度用常见的温度计图标，使用了别个大神的开源控件代码
   * 将独立的温度仪表盘和温度计控件，合二为一，成为自己独有的温湿度控件
   * 在此基础上自己实现了，当点击某个节点的温湿度对象的时候，在屏幕中心进行放大显示，移开鼠标以后自行关闭该对象的显示，也是重写了鼠标事件
   * 实现了分多个页面显示的功能，显示的节点总数可调整。，且布局不影响，不过这部分还需要改进，当节点数小于8的时候，页面数需要调整，
     小于4的时候还需要单独调整显示的行数
      
* 实现了多页面切换
   * 通过自定义标题栏，实现了在嵌入式Linux下的页面切换控件
   * 两个按钮，一个Label，用来显示标题内容
   * 和其他控件进行垂直布局的时候，需要指定Spacing和Margin以便布局更合理美观
    
    
  

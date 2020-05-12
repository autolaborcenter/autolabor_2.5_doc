### 目录

1. 如何获取源码
2. 导航程序文件路径
3. 导航程序启动/launch文件
4. 电脑控制机器人建图完毕，如何保存建图结果
5. 觉得目前最大速度(0.25m/s)有点慢，如何提高
6. APP与机器人/树莓派/ROS如何通信
7. Arduino源代码的主函数
8. 机器人/树莓派/ROS如何与Arduino通信
9. 开发语言
10. 如何将自己写的程序移植到树莓派上
11. Arduino源代码编译失败

***

##### 1、如何获取源码

凭订单号联系淘宝客服索要

lidar.taobao.com


##### 2、导航程序文件路径

/root/fireware/autolabor2.5

##### 3、导航程序启动/launch文件

/root/fireware/autolabor2.5/launch/lidar_nav.launch

电脑控制机器人建图完毕，如何保存建图结果

```
$ rosrun map_server map_saver -f  地图文件名
```

##### 4、觉得目前最大速度(0.25m/s)有点慢，如何提高

目前电机的转速为59rpm，最大速度已是电机极限。可通过更换电机提高速度，更换完电机后还需要修改程序。


##### 5、APP与机器人/树莓派/ROS如何通信

使用[rosbridge(rosbridge_suite)](http://wiki.ros.org/rosbridge_suite)功能包，通过websocket和udp的传输方式和ROS系统进行交互通信。

[通信协议- rosbridge v2.0 Protocol Specification](https://github.com/RobotWebTools/rosbridge_suite/blob/groovy-devel/ROSBRIDGE_PROTOCOL.md)

##### 6、Arduino源代码的主函数

RobotPIDDriver.ino文件323行，loop()

##### 7、机器人/树莓派/ROS如何与Arduino通信

使用[ros_arduino_bridge](http://wiki.ros.org/ros_arduino_bridge)功能包，通过串口指令的方式控制Arduino程序的运行。


##### 8、开发语言
C++ 或者 python

##### 9、如何将自己写的程序移植到树莓派上

1. 调试完毕，使用scp远程拷贝或者读卡器cp拷贝源代码至OS系统中
2. 编译

注：编译过程必须在树莓派中进行

##### 10、Arduino源代码编译失败

缺少PID库，查看视频2分10秒开始
http://www.autolabor.com.cn/lib/detail/2

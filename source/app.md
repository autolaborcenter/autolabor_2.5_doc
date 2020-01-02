# 基于ROSBridge和Unity3D开发

脱离ROS开发是指客户端程序运行不依赖于ROS系统独立运行的开发方式.

这样做的主要原因是为了避免对ROS的依赖.这样做会带来一些好处和一些缺点.

缺点: 需要中间层对ROS的数据包进行转换, 需要自己实现一些数据格式,增加一些工作量.

好处: 可以轻松把您的算法或控制程序轻松移植到各种平台上,可以用任何一种语言开发.

举例而言,Autolabor2.5无雷达版的手机程序是基于游戏引擎Unity3D开发的.

rosbridge是比较常见的ros中间件功能包,也有很多语言的客户端实现,比较出名的是[roslibjs](http://wiki.ros.org/roslibjs)

关于rosbridge的详细内容,可以参考其[官方网站](http://wiki.ros.org/rosbridge_suite?distro=kinetic)

下面以Autolabor2.5无雷达版附带的手机遥控程序为例,介绍在Unity3D开发环境下如何控制机器人.

# Unity3D开发示例

首先下载AutolaborMobileController的工程.

下载解压到随意目录中确保本机已经安装好 Unity3D 5.3以上开发环境.

如果没有安装,可以到[Unity3D官网下载](https://unity3d.com/cn).

Unity3D是一个免费的游戏开发引擎,具备轻松跨平台的特性,开发语言是C#

使用Unity3D打开刚才下载的工程.

打开TestScene 这个场景

在Canvas 这个物体上,有一个名叫RosTest1 的脚本,右键在弹出菜单内,选择Edit Script 即可打开.

首先映入眼帘的是Start函数, 这个函数会在场景启动时被自动调用

```
void Start() {
    ros = new ROSBridgeWebSocketConnection("ws://192.168.1.1", 9090);
    ros.AddPublisher(typeof(TeleopPublisher));
    ros.Connect();
}
```

首先创建了一个类型为RosBridgeWebSocket实例,并且指定了连接的地址和端口.

这个实例将处理所有与ros core的事物.

然后新增了一个Publisher对象,这个类型TeleopPublisher的对象类似一个模板,定义了我们将来要发布的topic的名字和类型,它的内部定义是这样的.


```
public class TeleopPublisher : ROSBridgePublisher {
    public static string GetMessageTopic() {
        return "cmd_vel";
    }
    public static string GetMessageType() {
        return "geometry_msgs/Twist";
    }
    public static string ToYAMLString(VelocityMsg msg) {
        return msg.ToYAMLString();
    }
}
```
我们需要控制机器人的移动,所以只要发布cmd\_vel这个话题就可以了,这个话题的数据类型是“geometry_msgs/Twist”


接下来是最核心的部分,接受屏幕触控操作,并向ros发布信息.

```
void Update() {
    Vector2 inputVector = new Vector2(CnInputManager.GetAxis("Horizontal"), CnInputManager.GetAxis("Vertical"));
    TwistMsg msg = new TwistMsg(new Vector3Msg(inputVector.ymaxLinearSpeed.value, 0.0, 0.0), new Vector3Msg(0.0, 0.0, -inputVector.xmaxAngularSpeed.value));
    ros.Publish(TeleopPublisher.GetMessageTopic(), msg);
    ros.Render();
}
```

Update是Unity3D内部定义的一个函数,它会在每次渲染时调用.

当这个方法被运行时,首先会从触控组件中取出摇杆偏移的坐标, 保存到inputVector里.

之后会把偏移的坐标进行简单的计算, 封装到一个Vector3Msg类型的实例里. 从而把屏幕偏移转化为速度和旋转角度.

再封装到一个TwistMsg实例中,交给RosBridge组件进行发布.

最后一个函数是 OnApplicationQuit, 功能也比较简单:在程序退出的时候,断开rosbridge的连接,代码如下.

```
void OnApplicationQuit() {
    if (ros != null) { ros.Disconnect(); }
}
```

 通过以上简单的代码,我们就可以实现用手机对Autolabor2.5无雷达版的控制了.
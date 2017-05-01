---
layout:     post
title:      "ABB arm keyboard control"
subtitle:   ""
date:       2017-05-01
author:     "YaoYao"
header-img: "img/in-post/default.jpg"
tags:
    - ROS
---

> 键盘控制六自由度的ABB机械臂，根据ROS-I的course 2.5b修改的。

### key\_teleop_node

创建key\_teleop_node节点，实现的功能是获取键盘指令，并转化为joint的运动信息，然后通过发送joint\_states message，控制机械臂各关节的运动。

course 2.5b里的代码实现的是GUI bar拖动控制abb关节运动。对于launch文件需要修改的地方：注释掉原launch文件中的 joint_state_publisher 节点，然后自己写一个key\_teleop_node 发布消息，消息类型是sensor_msgs::JointState 话题名称：/joint\_states。

![football-fans](https://mearshen.github.io/img/in-post/key_ABB.jpg)

key\_teleop_node 的[源码](https://github.com/mearshen/ros-industrial)已上传。几个地方稍微解释一下：	
 
1. launch文件中`<param name="use_gui" value="false"/> `设置为true，则表示打开bar控制条。

2. key\_teleop_node.cpp中：
`#define KEYCODE_Q 0x71   // 定义按键值   （按键输入时， shift+字母键，可以获取大写字母，即游戏中的加速功能）` 

3. key\_teleop_node.cpp中：
	>sensor_msgs::JointState cmdjs_;    
	>cmdjs_.header.stamp= ros::Time::now();   
	>pub_.publish(cmdjs_);    

	定义了一个消息变量，这个joint_state的消息,其中position变量是vector类型，每次发布前，一定要加上时间戳，否则接收节点不会识别为有效关节信息。

4. key\_teleop\_node.cpp 整体结构是添加了一个线程监听键盘事件，然后匀速修改关节位置，其中每个关节的位置都有最大最小限制（可以在robot的urdf文件中查看到关节的位置范围），然后将修改好的六个关节的位置赋值到msg中，并添加时间戳，最终用/joint\_states 发送出去。然后 launch文件中运行的robot\_state\_publisher节点会接收话题信息，并完成对robot 的控制，在rviz中显示出来。

5. 调试工具：
	>$ rqt_graph    
	>$ rostopic echo /joint_states


###参考资料：
1. http://wiki.ros.org/joint_state_publisher
2. http://docs.ros.org/jade/api/sensor_msgs/html/msg/JointState.html
3. http://blog.csdn.net/hcx25909/article/details/9004617
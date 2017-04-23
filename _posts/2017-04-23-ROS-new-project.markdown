---
layout:     post
title:      "ROS Create New Project"
subtitle:   ""
date:       2017-04-23
author:     "YaoYao"
header-img: "img/in-post/default.jpg"
tags:
    - ROS
---

> 一直想把之前的一些总结搬上来，但是几次打开笔记都发现之前的东西太杂，是个大工程。慢慢来吧，先传几个之前写的稍微有条理点的。。。

### ROS编译运行项目

如何编译运行ROS程序？这些交给 ROS 的catkin 编译系统来处理。   
一共有四个步骤：   
1，声明依赖库   
首先，我们需要声明程序所依赖的其他功能包。对于 c++ 程序而言，此步骤是必要的，以确保 catkin 能够向c++ 编译器提供合适的标记来定位编译功能包所需的头文件和链接库。   
为了给出依赖库，编辑包目录下的 CMakeLists.txt 文件。该文件的默认版本含有如下行：   
`find_package(catkin REQUIRED)`       
所依赖的其他 catkin 包可以添加到这一行的 COMPONENTS关键字后面，如下所示：   
`find_package(catkin REQUIRED COMPONENTS package-names)`    
对于 hello 例程，我们需要添加名为 roscpp 的依赖库，它提供了 ROS 的 C++ 客户端库。因此，修改后的 `find_package` 行如下所示：    
`find_package(catkin REQUIRED COMPONENTS roscpp)`    
我们同样需要在包的清单文件中列出依赖库，通过使用 build_depend （编译依赖）和run_depend （运行依赖）两个关键字实现：    
    `<build_depend>package-name</build_depend>`    
    `<run_depend>package-name</run_depend> `   
在我们的例程中， hello 程序在编译时和运行时都需要 roscpp 库，因此清单文件需要包括：    
`<build_depend>roscpp</build_depend>`    
`<run_depend>roscpp</run_depend> `   
然而，在清单文件中声明的依赖库并没有在编译过程中用到；如果你在此处忽略它们，你可能不会看到任何错误消息，直到发布你的包给其他人，他们可能在没有安装所需包的情况下编译你发布的包而导致报错。    

2，声明可执行文件    
接下来，我们需要在 CMakeLists.txt 中添加两行，来声明我们需要创建的可执行文件。其一般形式是：
`add_executable(executable-name source-files)`     
`target_link_libraries(executable-name ${catkin_LIBRARIES})`    
第一行声明了我们想要的可执行文件的文件名，以及生成此可执行文件所需的源文件列表。    
如果你有多个源文件，把它们列在此处，并用空格将其区分开。    
第二行告诉 Cmake 当链接此可执行文件时需要链接哪些库（在上面的 `find_package` 中定义）。      
如果你的包中包括多个可执行文件，为每一个可执行文件复制和修改上述两行代码。    
在我们的例程中，我们需要一个名为 hello 的可执行文件，它通过名为 hello.cpp 的源文件编译而来。    
所以我们需要添加如下几行代码到 CMakeLists.txt 中：      
`add_executable(hello hello.cpp)`       
`target_link_libraries(hello ${catkin_LIBRARIES})`         
通过catkin_create_pkg 创建的 CMakeLists.txt 的默认版本包含一些注释掉的提示，用于实现其他功能；对于大多数简单的程序，类似于此处展示的简单版本已经足够了。    

3，编译工作区    
一旦你的 CMakeLists.txt 文件设置好，你就可以编译你的工作区，使用如下命令来编译所有包中的所有可执行文件：    
`catkin_make`     
因为被设计成编译你的工作区中的所有包，这个命令必须从你的工作区目录运行。它将会完成一些配置步骤（尤其是你第一次运行此命令时），并且在你的工作区中创建 devel 和build 两个子目录。这两个新目录用于存放和编译相关的文件，例如自动生成的编译脚本、目标代码和可执行文件。如果你喜欢，当完成功能包的相关工作后（译者注：即完成了编写、调试、测试等一系列工作后，此时代码基本定型），可以放心地删除 devel 和build 两个子目录。如果有编译错误，你会在执行此步骤时看见它们。在更正它们以后，你可以重新运行 `catkin_make` 来完成编译工作。         
如果你看到来自 catkin_make 的错误，提示无法找到ros/ros.h头文件，或者 ros::init 等ROS 函数未定义的错误，最大的可能性是你的 CMakeLists.txt 没有正确声明对roscpp 的依赖。       

4，Sourcing setup.bash     
最后的步骤是执行名为setup.bash的脚本文件，它是`catkin_make`在你工作区的devel子目录下生成的。      
`source devel/setup.bash`    
这个自动生成的脚本文件设置了若干环境变量，从而使ROS能够找到你创建的功能包和新生成的可执行文件。除非目录结构发生变化，否则你只需要在每个终端执行此命令一次，即使你修改了代码并且用`catkin_make`执行了重编译。


### eclipse create ROS project：
1， 创建 catkin 程序包   
    `$ cd ~/catkin_ws/src`   
    `$ catkin_create_pkg beginner_tutorial std_msgs rospy roscp`   (# 依赖文件根据具体工程不同）   

2, 创建 launch目录   
    `$ roscd beginner_totorial`    
    `$ mkdir launch`

3, 创建 src目录    
    `$ mkdir -p ~/catkin_ws/src/beginner_totorial/src`     
 
4, 生成 eclipse 文件    
    `$ cd ~/catkin_ws`    
    `$ catkin_make --force-cmake -G"Eclipse CDT4 - Unix Makefiles" -DCMAKE_BUILD_TYPE=Debug -DCMAKE_ECLIPSE_MAKE_ARGUMENTS=-j8`

5， eclipse 中import project     
      导入工程后，可以在src 目录下 new 新的cpp文件，也可以直接复制文件到目录下。
      cpp编辑完成后，在CmakeList.txt 文件末尾 添加：`add_executable(speak src/speak.cpp)`

6， build project    
      Project-> bulid all    
      在Bin 文件夹下 生成了可执行文件。

7， run configuration    
      点击Run→ Run Configuration... → C/C++ Application (双击或者点击New),选择你所要运行的程序.     
      在EnvironmentTab中,新增(至少) `ROS_ROOT` 和 `ROS_MASTER_URI`。
      `ROS_ROOT` 和 `ROS_MASTER_URI` 的 value 可以通过 终端查询：    
      `$ echo $ROS_ROOT`   
      `$ echo $ROS_MASTER_URI`   

8， 运行    
      打开Terminal运行
      `$ roscore`   
      然后在Eclipse中分别运行speak. 依次可见到Console 有运行信息。
      说明ROS在Eclipse中环境搭建成功.并且ROSNode运行正常.


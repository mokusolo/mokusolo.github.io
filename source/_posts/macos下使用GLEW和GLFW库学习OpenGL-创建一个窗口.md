---
title: macos下使用GLEW和GLFW库学习OpenGL-创建一个窗口
date: 2017-04-19 20:47:21
tags: [openGL, macos]
---

-----------
[参考的教程地址](https://learnopengl-cn.readthedocs.io/zh/latest/)

一段帮助理解的话：

> 在我们画出出色的效果之前，首先要做的就是创建一个***OpenGL***上下文(Context)和一个用于显示的窗口。然而，这些操作在每个系统上都是不一样的，OpenGL有目的地从这些操作抽象(Abstract)出去。这意味着我们不得不自己处理创建窗口，定义OpenGL上下文以及处理用户输入。
>
>幸运的是，有一些库已经提供了我们所需的功能，其中一部分是特别针对OpenGL的。这些库节省了我们书写操作系统相关代码的时间，提供给我们一个窗口和上下文用来渲染。最流行的几个库有GLUT，SDL，SFML和GLFW。

> ***GLFW***是一个专门针对OpenGL的C语言库，它提供了一些渲染物体所需的最低限度的接口。它允许用户创建OpenGL上下文，定义窗口参数以及处理用户输入，这正是我们需要的。
>
> 到这里，我们仍然有一件事要做。因为OpenGL只是一个标准/规范，具体的实现是由驱动开发商针对特定显卡实现的。由于OpenGL驱动版本众多，它大多数函数的位置都无法在编译时确定下来，需要在运行时查询。任务就落在了开发者身上，开发者需要在运行时获取函数地址并将其保存在一个函数指针中供以后使用。取得地址的方法因平台而异，代码非常繁琐，我们需要对每个可能使用的函数都要重复这个过程。幸运的是，有些库能简化此过程，其中***GLEW***是目前最新，也是最流行的库。

[GLFW库地址](http://www.glfw.org/download.html)
[GLEW库地址](http://glew.sourceforge.net/index.html)

根据平台不同可编译源码，但是macos的brew可以很方便的安装。
```bash
brew install glfw3
brew install glew
```
安装后查看lib库和头文件地址，版本号可能不一致：
```bash
ls /usr/local/Cellar/glew/2.0.0/lib/
ls /usr/local/Cellar/glfw/3.2.1/lib/
ls /usr/local/Cellar/glew/2.0.0/include/
ls /usr/local/Cellar/glfw/3.2.1/include/
```

 - 打开xcode，新建项目
 - 该项目的Build Phases界面，Link Binary With Libraries中，添加库
> OpenGL.framework
> libglfw.3.2.dylib(add other, shift+command+g, /usr/local/, 到上面glfw的lib库地址添加)
> libGLEW.2.0.0.dylib(add other, shift+command+g, /usr/local/, 到上面glew的lib库地址添加)

 - 该项目的Build Settings界面，Search Paths中，添加头文件路径和lib库路径，对应上面的头文件路径和lib库文件路径
 - 编辑cpp文件

```cpp
//
//  main.cpp
//  opengl_learning
//

#include <iostream>
// GLEW
#include <GL/glew.h>

// GLFW是一个专门针对OpenGL的C语言库，它提供了一些渲染物体所需的最低限度的接口。它允许用户创建OpenGL上下文，定义窗口参数以及处理用户输入。
#include <GLFW/glfw3.h>

// 窗口定义
const GLuint WIDTH = 800, HEIGHT = 600;

// 主函数
int main(int argc, const char * argv[]) {
    // 初始化GLFW
    if(!glfwInit())
    {
        throw std::runtime_error("glfwInit failed");
    }

    // mac系统必须添加
    glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);

    //
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_RESIZABLE, GL_FALSE);

    // 创建一个窗口对象，这个窗口对象存放了所有和窗口相关的数据，而且会被GLFW的其他函数频繁地用到。
    GLFWwindow* window = glfwCreateWindow(WIDTH, HEIGHT, "Create a window", NULL, NULL );
    if(window == nullptr)
    {
        std::cout << "Failed to create GLFW window" << std::endl;
        glfwTerminate();
        return -1;
    }

    // 设置一个“当前”openGL上下文给刚创建的窗口
    glfwMakeContextCurrent(window);

    // 让GLEW在管理OpenGL的函数指针时更多地使用现代化的技术，如果把它设置为GL_FALSE的话可能会在使用OpenGL的核心模式时出现一些问题。
    glewExperimental = GL_TRUE;

    // GLEW是用来管理OpenGL的函数指针的，所以在调用任何OpenGL的函数之前我们需要初始化GLEW。
    if(glewInit() != GLEW_OK)
    {
        std::cout<<"Failed to initialize GLEW"<<std::endl;
        return -1;
    }

    if(!GLEW_VERSION_3_3)
    {
        throw std::runtime_error("openGL 3.3 API is not available");
    }

    // 告诉OpenGL渲染窗口的尺寸大小，这样OpenGL才只能知道怎样相对于窗口大小显示数据和坐标。我们可以通过调用glViewport函数来设置窗口的维度(Dimension)：
    int width,height;
    glfwGetFramebufferSize(window, &width, &height);
    glViewport(0, 0, width, height);

    // 程序循环。我们每次循环的开始前检查一次GLFW是否被要求退出，如果是的话该函数返回true然后游戏循环便结束了，之后为我们就可以关闭应用程序了。
    while(!glfwWindowShouldClose(window))
    {
        // 检查有没有触发什么事件（比如键盘输入、鼠标移动等）
        glfwPollEvents();

        // 设置清空屏幕所用的颜色。在这里，我们将屏幕设置为了类似黑板的深蓝绿色。
        glClearColor(0.2f, 0.3f, 0.3f, 1.0f);

        // 在每个新的渲染迭代开始的时候我们总是希望清屏，否则我们仍能看见上一次迭代的渲染结果（这可能是你想要的效果，但通常这不是）。我们可以通过调用glClear函数来清空屏幕的颜色缓冲，它接受一个缓冲位(Buffer Bit)来指定要清空的缓冲，可能的缓冲位有GL_COLOR_BUFFER_BIT，GL_DEPTH_BUFFER_BIT和GL_STENCIL_BUFFER_BIT。由于现在我们只关心颜色值，所以我们只清空颜色缓冲。
        glClear(GL_COLOR_BUFFER_BIT);

        //交换颜色缓冲（它是一个储存着GLFW窗口每一个像素颜色的大缓冲），它在这一迭代中被用来绘制，并且将会作为输出显示在屏幕上。
        glfwSwapBuffers(window);
    }

    // 释放GLFW分配的内存。
    glfwTerminate();
    return 0;
}


```

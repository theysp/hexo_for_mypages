---
title: 实践路上遍布荆棘-Opengl常见问题记录
date: 2017-09-20 10:30:26
categories: "技术积累" #文章分類目錄 可以省略
tags:
    - opengl
    - Opengl常见错误
---

说多了都是泪啊。
三维渲染程序编译通过却总是看不见图像，看见图像了颜色又不对，出Opengl标准的想象中的boss们的小人被自己在心里默默扎了无数遍。。。
之后常见的错误都会记录在这里。

## 初步诊断

这里记录一下常见的问题可能的原因，可能的解决方法：

1. 渲染一帧过程中切换shader不起作用：八成是新shader未通过编译，导致该shader无效，所以切换到该shader的命令也无效了。

2. 视野内无物体：有可能是该帧初始化时没有清除缓存，也有可能是视角的转换矩阵设置错误。

3. 渲染到屏幕缓存的过程中插入一段渲染到内存缓存的命令（可能是用于生成纹理），然后后面的渲染就看不见了：渲染到缓存时，一般会调整视窗范围，在后续的渲染到屏幕缓存命令前需要重置视窗为屏幕整体。

## Shader的uniform变量一定要初始化

不然你永远得不到你想要的东西。
比如顶点shader中，你加一个modelspacetrans的矩阵吧，这个矩阵如果在程序中不初始化（gluniformxxx)，那么等待你的可能是未知的随机矩阵。

## Shader中，函数返回vec一定要显示定义vec
比如下面：
```c++


vec3 FloatToRGBData(float attenuationRate){
	int tmpI = floatBitsToInt(attenuationRate);
	tmpI = floatBitsToInt(0.0342);
	int ir = (0xff000000 & tmpI)>>24; //exp part
	int ig = (0xff0000 & tmpI)>>16;
	int ib = (0xff00 & tmpI)>>8;
	float r = ir/256.0;
	float g = ig/256.0;
	float b = ib/256.0;
	return vec3(r,g,b);  //正确写法
}

vec3 FloatToRGBData(float attenuationRate){
	int tmpI = floatBitsToInt(attenuationRate);
	tmpI = floatBitsToInt(0.0342);
	int ir = (0xff000000 & tmpI)>>24; //exp part
	int ig = (0xff0000 & tmpI)>>16;
	int ib = (0xff00 & tmpI)>>8;
	float r = ir/256.0;
	float g = ig/256.0;
	float b = ib/256.0;
	return (r,g,b);  //错误写法
}

```

## 渲染每个场景前，一定也要初始化要渲染的Buffer

不然会出现这样的情况，比如Buffer里的深度信息都是零，那么你什么也渲染不上去，或者出现各种破烂哦。
所以两个函数要牢记心间：

``` c++

void glClearColor(0.0f, 0.0f, 0.0f, 1.0f); 
void glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT); 
```

## 勤输出出错信息，勤观察出错信息

opengl提供了一些用于出错信息输出的接口。

1. 一个回调函数，opengl会在所有出错的时候调用这个回调函数

``` c++
//在初始化时添加回调函数
glfwSetErrorCallback(gl_error_callback);
//需要自行实现的回调函数本体
void gl_error_callback(int code, const char* info) {
	cout << info << endl;
}
```

2. 第二个回调函数，用于在Debug时输出详细信息

``` c++
//在初始化时添加回调函数
glDebugMessageCallback(glDebugOutput, nullptr);
//启用debug信息
glDebugMessageControl(GL_DONT_CARE, GL_DONT_CARE, GL_DONT_CARE, 0, nullptr, GL_TRUE);
//需要自行实现的回调函数本体
void APIENTRY glDebugOutput(GLenum source,
	GLenum type,
	GLuint id,
	GLenum severity,
	GLsizei length,
	const GLchar *message,
	const void *userParam)
{
	// ignore non-significant error/warning codes
	if (id == 131169 || id == 131185 || id == 131218 || id == 131204) return;

	std::cout << "---------------" << std::endl;
	std::cout << "Debug message (" << id << "): " << message << std::endl;
	switch (source)
	{
	case GL_DEBUG_SOURCE_API_ARB:             std::cout << "Source: API"; break;
	case GL_DEBUG_SOURCE_WINDOW_SYSTEM_ARB:   std::cout << "Source: Window System"; break;
	case GL_DEBUG_SOURCE_SHADER_COMPILER_ARB: std::cout << "Source: Shader Compiler"; break;
	case GL_DEBUG_SOURCE_THIRD_PARTY_ARB:     std::cout << "Source: Third Party"; break;
	case GL_DEBUG_SOURCE_APPLICATION_ARB:     std::cout << "Source: Application"; break;
	case GL_DEBUG_SOURCE_OTHER_ARB:           std::cout << "Source: Other"; break;
	} std::cout << std::endl;

	switch (type)
	{
	case GL_DEBUG_TYPE_ERROR_ARB:               std::cout << "Type: Error"; break;
	case GL_DEBUG_TYPE_DEPRECATED_BEHAVIOR_ARB: std::cout << "Type: Deprecated Behaviour"; break;
	case GL_DEBUG_TYPE_UNDEFINED_BEHAVIOR_ARB:  std::cout << "Type: Undefined Behaviour"; break;
	case GL_DEBUG_TYPE_PORTABILITY_ARB:         std::cout << "Type: Portability"; break;
	case GL_DEBUG_TYPE_PERFORMANCE_ARB:         std::cout << "Type: Performance"; break;
	case GL_DEBUG_TYPE_OTHER_ARB:               std::cout << "Type: Other"; break;
	} std::cout << std::endl;

	switch (severity)
	{
	case GL_DEBUG_SEVERITY_HIGH:         std::cout << "Severity: high"; break;
	case GL_DEBUG_SEVERITY_MEDIUM:       std::cout << "Severity: medium"; break;
	case GL_DEBUG_SEVERITY_LOW:          std::cout << "Severity: low"; break;
	case GL_DEBUG_SEVERITY_NOTIFICATION: std::cout << "Severity: notification"; break;
	} std::cout << std::endl;
	std::cout << std::endl;
}
```

3. 可以自己编写一个报错信息检查函数，可插入代码的任意位置，报告当前位置最后一个错误

``` c++
GLenum glCheckError_(const char *file, int line)
{
	GLenum errorCode;
	while ((errorCode = glGetError()) != GL_NO_ERROR)
	{
		std::string error;
		switch (errorCode)
		{
		case GL_INVALID_ENUM:                  error = "INVALID_ENUM"; break;
		case GL_INVALID_VALUE:                 error = "INVALID_VALUE"; break;
		case GL_INVALID_OPERATION:             error = "INVALID_OPERATION"; break;
		case GL_OUT_OF_MEMORY:                 error = "OUT_OF_MEMORY"; break;
		case GL_INVALID_FRAMEBUFFER_OPERATION: error = "INVALID_FRAMEBUFFER_OPERATION"; break;
		}
		if(error.size()>0)
			std::cout << error << " | " << file << " (" << line << ")" << std::endl;
	}
	return errorCode;
}

```


# vcpkg安装及使用(Windows)

## 安装说明

本文所述的vcpkg安装是在Windows环境下进行的，目的是方便直接在本地环境学习C++库、数据结构、算法等，无需远程Linux环境。

推荐的其他相关文章：CLione+CMake+vcpkg在Windows下进行C++开发、vcpkg安装及使用(Linux)

> 没有连接则是还没有更新

### vcpkg简介

vcpkg是微软官方提供的一款跨平台的C++第三方库管理工具，实现类似于python-pip工具的功能，对依赖包进行一键安装，极大的提升了开发效率。

### 需求

- Windows 7 或更新的版本
- [Git][https://git-scm.com/downloads]
- [Visual Studio 2015][https://visualstudio.microsoft.com/zh-hans/] 或更新的版本（包含英文语言包）

> 热忱提示：
>
> 1、包含英文语言包很重要
>
> 2、建议vcpkg配合CMake工具使用，这样效率是最高的

## 安装步骤

### 获取源码

```cmd
# 克隆源码
> git clone https://github.com/microsoft/vcpkg
# 执行 bootstrap.bat 脚本
> .\vcpkg\bootstrap-vcpkg.bat
```

建议使用例如 `C:\src\vcpkg` 或 `C:\dev\vcpkg` 的安装目录，否则可能遇到某些库构建系统的路径问题。

安装完成后，vcpkg目录下应该包含可执行程序`vcpkg.exe`

### 将vcpkg添加到本机环境变量中

1. 复制vcpkg的安装目录，在这里我的路径为`C:\Users\Liang\dev\vcpkg`
2. 右键`我的电脑`->`属性`->`高级系统设置`->`环境变量`
3. 编辑用户或系统组下的`Path`变量，将步骤1中的路径加入，保存
4. 重启生效

> 热忱提示：
>
> 1、使用powershell或cmd直接输入vcpkg，若有帮助输出，则表示安装成功
>
> 2、经常在Linux环境下工作的朋友建议使用powershell

### 使用vcpkg

使用以下命令安装您的项目所需要的库

```powershell
> vcpkg install [packages name]
# 如果未配置环境变量，请使用绝对/相对路径
```

可以使用 `search` 子命令来查找vcpkg中集成的库:

```powershell
> vcpkg search [search term]
```

若希望在全局使用vcpkg，运行以下命令 (首次启动需要管理员权限)

```powershell
> vcpkg integrate install
```

在此之后，你可以创建一个项目 (或打开已有的项目)。
在项目中，所有已安装的库均可立即使用 `#include` 包含需使用的库的头文件并无需添加额外配置。

为了在IDE以外在cmake中使用vcpkg，需要使用以下工具链文件:

```powershell
> cmake -B [build directory] -S . -DCMAKE_TOOLCHAIN_FILE=[path to vcpkg]/scripts/buildsystems/vcpkg.cmake
> cmake --build [build directory]
```

其中`-DCMAKE_TOOLCHAIN_FILE=[path to vcpkg]/scripts/buildsystems/vcpkg.cmake`的目的是将vcpkg官方提供的cmake引入我们的工程项目，这样的话`vcpkg.cmake`可以自动帮助我们包含头文件以及找到相关的库文件。

虽然配置了`vcpkg.cmake`，但是在CMakeList.txt中任然需要通过以下语句来指定需要使用的第三方库

```cmake
# 寻找第三方库
find_package(<package_name> CONFIG REQUIRED)
# 连接库
target_link_libraries(<project_name> PRIVATE <package_name>:<package_module>)
```


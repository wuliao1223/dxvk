DXVK
基于 Vulkan 的 Direct3D 8/9/10/11 转换层，允许使用 Wine 在 Linux 上运行 3D 应用程序。

有关项目的当前状态，请参阅项目 wiki。

最新的开发版本可在此处找到。

发布版本可以在这里找到。

如何使用
为了将从发布页面获得的 DXVK 包安装到给定的 wine 前缀中，请将 DLL 复制或符号链接到以下目录中，如下所示，然后打开winecfg并在“库”选项卡下手动添加、、和的nativeDLL 覆盖。d3d8d3d9d3d10cored3d11dxgi

在默认的 Wine 前缀中，其内容如下：

export WINEPREFIX=/path/to/wineprefix
cp x64/*.dll $WINEPREFIX/drive_c/windows/system32
cp x32/*.dll $WINEPREFIX/drive_c/windows/syswow64
winecfg
对于纯 32 位 Wine 前缀（非默认），32 位 DLL 请转到以下system32目录：

export WINEPREFIX=/path/to/wineprefix
cp x32/*.dll $WINEPREFIX/drive_c/windows/system32
winecfg
通过启用 HUD 来验证您的应用程序使用 DXVK 而不是 wined3d（参见下面的注释）。

为了从前缀中删除 DXVK，请删除 DLL 和 DLL 覆盖，然后运行wineboot -u以恢复原始 DLL 文件。

Steam Play、Lutris、Bottles、Heroic Launcher 等工具在启用后将自动处理 dxvk 的设置。

DLL 依赖项
下面列出了使用 DXVK 与任何单一 API 的 DLL 要求。

d3d8：d3d8.dll和d3d9.dll
d3d9：d3d9.dll
d3d10: d3d10core.dll，d3d11.dll和dxgi.dll
d3d11：d3d11.dll和dxgi.dll
Vulkan 驱动程序说明
在报告问题之前，请检查当前驱动程序状态的Wiki页面，并确保您运行的驱动程序版本足够新，适合您的硬件。

在线多人游戏
在多人游戏中操纵 Direct3D 库可能被视为作弊行为，并可能导致您的帐户被封禁。这也适用于带有嵌入式或专用多人部分的单人游戏。使用时风险自负。

抬头显示器
环境DXVK_HUD变量控制可以显示帧速率和一些统计计数器的 HUD。它接受以下选项的逗号分隔列表：

devinfo：显示GPU的名称和驱动程序版本。
fps：显示当前的帧速率。
frametimes：显示帧时间图。
submissions：显示每帧提交的命令缓冲区数量。
drawcalls：显示每帧的绘制调用次数和渲染次数。
pipelines：显示图形和计算管道的总数。
descriptors：显示描述符池和描述符集的数量。
memory：显示分配和使用的设备内存量。
allocations：显示详细的内存块子分配信息。
gpuload：显示估计的 GPU 负载。可能不准确。
version：显示 DXVK 版本。
api：显示应用程序使用的D3D功能级别。
cs：显示工作线程统计信息。
compiler：显示着色器编译器活动
samplers：显示当前使用的采样器对的数量[仅限 D3D9]
ffshaders：显示当前从固定功能状态生成的着色器数量[仅限 D3D9]
swvp：显示设备是否在软件顶点处理模式下运行[仅限 D3D9]
scale=x：按比例缩放 HUD x（例如1.5）
opacity=y：按 因子调整 HUD 不透明度y（例如0.5，1.0完全不透明）。
此外，DXVK_HUD=1具有与 相同的效果DXVK_HUD=devinfo,fps，并DXVK_HUD=full启用所有可用的 HUD 元素。

日志
与 Wine 一起使用时，DXVK 会将日志消息打印到stderr。此外，还可以通过设置变量来选择性地生成独立日志文件DXVK_LOG_PATH，其中给定目录中的日志文件将被称为app_d3d11.log，app_dxgi.log等等，其中app是游戏可执行文件的名称。

在 Windows 上，日志文件将默认创建在游戏的工作目录中，该目录通常位于游戏可执行文件旁边。

帧率限制
环境变量DXVK_FRAME_RATE可用于限制帧速率。值为0不限制帧速率，而任何正值将限制渲染为每秒给定的帧数。或者，可以使用配置文件。

设备筛选器
某些应用程序不提供选择其他 GPU 的方法。在这种情况下，DXVK 可能会被强制使用给定的设备：

DXVK_FILTER_DEVICE_NAME="Device Name"选择具有匹配 Vulkan 设备名称的设备，可以使用 等工具检索vulkaninfo。匹配子字符串，因此如果完整设备名称为“AMD RADV VEGA10 (LLVM 9.0.0)”，则支持“VEGA”或“AMD RADV VEGA10”。如果子字符串与多个设备匹配，则将使用第一个匹配的设备。
注意：如果设备过滤器配置不正确，它可能会过滤掉所有设备，并且应用程序将无法创建 D3D 设备。

调试
以下环境变量可用于调试目的。

VK_INSTANCE_LAYERS=VK_LAYER_KHRONOS_validation启用 Vulkan 调试层。强烈推荐用于解决渲染问题和驱动程序崩溃问题。需要在主机系统上安装 Vulkan SDK。
DXVK_LOG_LEVEL=none|error|warn|info|debug控制消息记录。
DXVK_LOG_PATH=/some/directory更改日志文件的存储路径。设置为none可完全禁用日志文件创建，但不禁用日志记录。
DXVK_DEBUG=markers|validation允许使用扩展VK_EXT_debug_utils来转换性能事件标记，或者启用 Vulkan 验证。
DXVK_CONFIG_FILE=/xxx/dxvk.conf设置配置文件的路径。
DXVK_CONFIG="dxgi.hideAmdGpu = True; dxgi.syncInterval = 0"可以用来通过环境设置配置变量，而不是使用相同语法的配置文件。;用作分隔符。
图形管道库
在支持VK_EXT_graphics_pipeline_libraryVulkan 的驱动程序上，着色器将在游戏加载其 D3D 着色器时进行编译，而不是在绘制时进行编译。与之前的系统相比，这减少或消除了许多游戏中的着色器编译卡顿。

在加载屏幕或菜单中加载着色器的游戏中，这可能会导致长时间的 CPU 利用率过高，尤其是在较弱的 CPU 上。对于受影响的游戏，建议等待着色器编译完成后再开始游戏，以避免卡顿和性能低下。可以使用 来监控着色器编译器活动DXVK_HUD=compiler。

该功能很大程度上取代了状态缓存。

注意：在绘制时只加载 D3D 着色器的游戏（例如大多数虚幻引擎游戏）仍然会出现一些卡顿，尽管比没有此功能时要小一些。

状态缓存
DXVK 默认缓存管道状态，这样着色器就可以在应用程序的后续运行中提前重新编译，即使驱动程序自己的着色器缓存在此期间失效。此缓存默认启用，通常可以减少卡顿。

以下环境变量可用于控制缓存：

DXVK_STATE_CACHE：控制状态缓存。支持以​​下值：
disable：完全禁用缓存。
reset：清除缓存文件。
DXVK_STATE_CACHE_PATH=/some/directory指定放置缓存文件的目录。默认为应用程序的当前工作目录。
此功能主要仅适用于不支持的系统VK_EXT_graphics_pipeline_library

构建说明
为了提取构建所需的所有子模块，请使用以下命令克隆存储库：

git clone --recursive https://github.com/doitsujin/dxvk.git
要求：
wine 7.1或更新版本
Meson构建系统（至少 0.58 版本）
Mingw-w64编译器和头文件（至少 10.0 版本）
glslang编译器
构建 DLL
简单的方法
在 DXVK 目录中，运行：

./package-release.sh master /your/target/directory --no-package
dxvk-master这将在中创建一个文件夹/your/target/directory，其中包含 32 位和 64 位版本的 DXVK，可以按照与上面提到的发布版本相同的方式进行设置。

为了保留构建目录以供开发使用，请传递--dev-build给脚本。此选项意味着--no-package。在对源代码进行更改后，您可以执行以下操作来重建 DXVK：

# change to build.32 for 32-bit
cd /your/target/directory/build.64
ninja install
手动编译
# 64-bit build. For 32-bit builds, replace
# build-win64.txt with build-win32.txt
meson setup --cross-file build-win64.txt --buildtype release --prefix /your/dxvk/directory build.w64
cd build.w64
ninja install
D3D8、D3D9、D3D10、D3D11 和 DXGI DLL 将位于/your/dxvk/directory/bin。

构建故障排除
DXVK 需要 mingw-w64 构建环境的线程支持。如果缺少此功能，您可能会看到“错误：‘std::cv_status’ 尚未声明”或类似的线程相关错误。

在 Debian 和 Ubuntu 上，可以使用支持线程的 posix 替代方案解决此问题。例如，从以下命令中选择 posix 替代方案：

update-alternatives --config x86_64-w64-mingw32-gcc
update-alternatives --config x86_64-w64-mingw32-g++
update-alternatives --config i686-w64-mingw32-gcc
update-alternatives --config i686-w64-mingw32-g++
对于非基于 Debian 的发行版，请确保您的 mingw-w64-gcc 交叉编译器--enable-threads=posix在配置期间已启用。如果您的发行版确实附带了 mingw-w64-gcc 二进制文件，--enable-threads=win32您可能需要在本地重新编译或在发行版的 bugtracker 上打开 bug 来请求它。

DXVK 原生
DXVK Native 是 DXVK 的一个版本，允许其在没有 Wine 的情况下本地使用。

这主要用于游戏和应用程序端口，以避免必须编写另一个渲染后端，或者帮助在开发过程中启动端口。

发布版本是使用 Steam Runtime 构建的。

它是如何工作的？
DXVK Native 用与平台和框架无关的替代品取代了某些 Windows 主义，例如HWNDs 可以变成SDL_Window*s 等等。只需添加另一个 WSI 后端即可做到这一点。

注意： DXVK Native 需要通过环境变量明确设置后端DXVK_WSI_DRIVER。当前内置选项为SDL3、SDL2和GLFW。

DXVK Native 附带一组 D3D9/11 所需的 Windows 标头定义和 D3D9/11 的 MinGW 标头。在大多数情况下，它最终会与您的渲染器即插即用，但可能会存在某些初期问题，例如：

__uuidof(type)受支持，但__uuidof(variable)不受支持。请__uuidof_var(variable)改用。

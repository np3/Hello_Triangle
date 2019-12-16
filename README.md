Hello Triangle Vulkan DXGI interop demo
=========================

This is a Hello World style application for graphical Vulkan showing qck and
dyrty interop with DXGI. This demo uses DXGI swapchain and imports its Images
into Vulkan.
Then it renders a RGB shaded equilateral triangle (well, if the resolution is a
square).

If you appriciate any of my free work or help (e.g. hosting this), you can send
me some sweet sweet money:

<a href="https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=6NRLHTNEP8GH8&item_name=For+Petr+Kraus+%28aka+krOoze%29+if+you+appreciate+any+of+my+work+done+for+free.&currency_code=USD&source=url">
<img width="145" height="30" src="http://vulkan.hys.cz/donate.png" alt="Donate $ to krOoze" /></a>
<a href="https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=6NRLHTNEP8GH8&item_name=For+Petr+Kraus+%28aka+krOoze%29+if+you+appreciate+any+of+my+work+done+for+free.&currency_code=EUR&source=url">
<img width="145" height="30" src="http://vulkan.hys.cz/donateEur.png" alt="Donate € to krOoze" /></a>

Branches
-----------------

The git branches demonstrate some basic Vulkan features which can be easily
grafted on this basic example (it is nice to see a diff of what exactly needs to
be changed to make it work). Their `README.md` should be edited to reflect the
changes.

| branch | diff link | description |
|---|---|---|
| [master](https://github.com/krOoze/Hello_Triangle/tree/master)| -- | Your regular Hello Triangle app, and base for the others
| [MSAA](https://github.com/krOoze/Hello_Triangle/tree/MSAA)  | [diff](https://github.com/krOoze/Hello_Triangle/compare/MSAA) | Antialiasing (i.e. Vulkan's multisample image resolve) |
| [queue_transfer](https://github.com/krOoze/Hello_Triangle/tree/queue_transfer) | [diff](https://github.com/krOoze/Hello_Triangle/compare/queue_transfer) | Transfer of `EXCLUSIVE` image between queue families (separate graphics and compute queue family) |
| [vertex_offset](https://github.com/krOoze/Hello_Triangle/tree/vertex_offset) | [diff](https://github.com/krOoze/Hello_Triangle/compare/vertex_offset) | Demonstrates how offset in `vkCmdBindVertexBuffers()` works |
| [dxgi_interop](https://github.com/krOoze/Hello_Triangle/tree/dxgi_interop) | [diff](https://github.com/krOoze/Hello_Triangle/compare/dxgi_interop) | Shows how to import swapchain images from DXGI (Direct3D 12 Swapchain) |

Requirements
----------------------------

**OS**: Windows or Linux  
**Language**: C++14  
**Build environment**: (latest) LunarG SDK (requires `VULKAN_SDK` variable, and `glslc` binary built)  
**Build environment[Windows]**: Visual Studio, Cygwin, or MinGW (or IDEs running on top of them)  
**Target Environment**: installed (latest) Vulkan capable drivers (to see anything)  
**Target Environment**: Windows 10 (DirectX 12)

Files
----------------------------------

| file | description |
|---|---|
| [doc/synchronizationTutorial.md](doc/synchronizationTutorial.md) | Short explanation of Vulkan synchronization in the context of trivial applications |
| external/glfw/ | GLFW git submodule |
| src/HelloTriangle.cpp | The app souce code including `main` function |
| src/CompilerMessages.h | Allows to make compile-time messages shown in the compiler output |
| src/EnumerateScheme.h | A scheme to unify usage of most Vulkan `vkEnumerate*` and `vkGet*` commands |
| src/ErrorHandling.h | `VkResult` check helpers + `VK_EXT_debug_report` extension related stuff |
| src/ExtensionLoader.h | Functions handling loading of Vulkan extension commands |
| src/LeanWindowsEnvironment.h | Included conditionally by `VulkanEnvironment.h` and includes lean `windows.h` header |
| src/Vertex.h | Just simple Vertex definitions |
| src/VulkanEnvironment.h | Includes `vulkan.h` and the necessary platform headers |
| src/VulkanReflection.h | Reflection of Vulkan; e.g. convert VK enumerants to strings |
| src/WSI/DxgiWsi.h | DXGI implementation as a Vulkan swapchain |
| src/shaders/hello_triangle.vert | The vertex shader program in GLSL |
| src/shaders/hello_triangle.frag | The fragment shader program in GLSL |
| .gitignore | Git filter file ignoring most probably outputs messing the local repo |
| .gitmodules | Git submodules file describing the dependency on GLFW |
| CMakeLists.txt | CMake makefile |
| CONTRIBUTING.md | Extra info about contributing code, ideas and bug reports |
| LICENSE | Copyright licence for this project |
| README.md | This file |

Config
---------------------------------------

You can change the application configuration by simply changing following
variables in `HelloTriangle.cpp`.

| config variable | purpose | default |
|---|---|---|
| `debugVulkan` | Turns debug output and validation layers on | tries to choose based on compiler debug mode |
| `debugAmount` | Which kinds of debug messages will be shown | `WARNING` \| `PERFORMANCE_WARNING` \| `ERROR` |
| `fpsCounter` | Enable FPS counter via `VK_LAYER_LUNARG_monitor` layer | `true` |
| `initialWindowWidth` | The initial width of the rendered window | `800` |
| `initialWindowHeight` | The initial height of the rendered window | `800` |
| `presentMode` | The presentation mode of Vulkan used in swapchain | `VK_PRESENT_MODE_FIFO_KHR` <sup>1</sup>|
| `clearColor` | Background color of the rendering | gray (`{0.1f, 0.1f, 0.1f, 1.0f}`) |
| `vertexShaderFilename` | The file with the SPIR-V vertex shader program | `triangle.vert.spv` |
| `fragmentShaderFilename` | The file with the SPIR-V fragment shader program | `triangle.frag.spv` |

<sup>1</sup> I preferred `VK_PRESENT_MODE_IMMEDIATE_KHR` before but it tends to
make coil whine because of the exteme FPS (which could be unnecessarily
dangerous to the PCB in long term).

Build
----------------------------------------------

First just get everything:

    $ git clone --recurse-submodules https://github.com/krOoze/Hello_Triangle.git

In many platforms CMake style build should work just fine:

    $ cmake -G"Your preferred generator"

or even just

    $ cmake .

Then use `make`, or the generated Visual Studio `*.sln`, or whatever it created.

There are two cmake options (supplied by `-D`):
 - `WSI` -- ignored. DXGI is used.
 - `TODO` -- set this to `OFF` to remove TODO messages during compilation.

 You also might want to add `-DCMAKE_BUILD_TYPE=Debug`.

<hr />

The code base is quite simple and it can be built manually. It only assumes that
the `src` folder is in the include path, that you link the GLFW dependency, and
that you compile the GLSL shader files into SPIR-V.

In Visual Studio you can simply import the code and add things mentioned above.
Either "console" or "window" subsystem is a valid choice.

In Linux distro you would do e.g.:

    $ g++ --std=c++14 -Wall -m64 -D_DEBUG -DNO_TODO -I$VULKAN_SDK/include -I./src/ -o./HelloTriangle src/HelloTriangle.cpp -ldl -L$VULKAN_SDK/lib -lvulkan -lglfw

GLSL shaders can be compiled using `glslc` from LunarG Vulkan SDK like so:  
On Windows-like environment:

    %VULKAN_SDK%/Bin/glslc -mfmt=c -o ./src/shaders/hello_triangle.vert.spv.inl ./src/shaders/hello_triangle.vert
    %VULKAN_SDK%/Bin/glslc -mfmt=c -o ./src/shaders/hello_triangle.frag.spv.inl ./src/shaders/hello_triangle.frag

Or on Unix-like environment you would use just `$VULKAN_SDK` instead:

    $VULKAN_SDK/Bin/glslc -mfmt=c -o ./src/shaders/hello_triangle.vert.spv.inl ./src/shaders/hello_triangle.vert
    $VULKAN_SDK/Bin/glslc -mfmt=c -o ./src/shaders/hello_triangle.frag.spv.inl ./src/shaders/hello_triangle.frag

There are annoying (on purpose) TODOs generated on build. They can be disabled
by defining `NO_TODO` preprocessor macro.

Using GLFW is optional. You may choose another windowing platform by modifying
`VulkanEnvironment.h`. By default all platforms use GLFW.

Run
------------------------

You just run it as you would anything else.

<kbd>Esc</kbd> does terminate the app.  
<kbd>Alt</kbd> + <kbd>Enter</kbd> toggles fullscreen (might not work on some WSI
platforms.

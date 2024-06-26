这段时间项目要求将一个cpp程序运行在[linux操作系统](https://so.csdn.net/so/search?q=linux%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F&spm=1001.2101.3001.7020)上，其中程序用到了SDL2的相关的头文件和库。在安装SDL2的包的时候遇到了不少问题，借此机会记录一下。
安装过程参考了[Linux下编译安装SDL2](https://blog.csdn.net/xiaolong1126626497/article/details/105761548?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162916320016780255294304%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=162916320016780255294304&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_v2~hot_rank-10-105761548.first_rank_v2_pc_rank_v29&utm_term=sdl2%E5%AE%89%E8%A3%85&spm=1018.2226.3001.4187)这篇博客。
**0. 安装PulseAudio和ALSA**
非常重要！！必须一开始先安装好。原先我没有做这一步，好不容易把后续步骤做完了。但在调用SDL_OpenAudio函数时失败，报错原因是no such audio device。 后来找到原因是没有获得PulseAudio和ALSA的支持，在安装这两个音频接口支持后还得把后续的步骤重新做一遍，花了很长时间。
```
sudo apt-get install libasound2-dev libpulse-dev         一定要最开始做！！
```
**1. 下载SDL2源码库**（[下载地址](https://www.linuxfromscratch.org/blfs/view/cvs/multimedia/sdl2.html)）
**2. 安装环境配置**
```
$ tar xvf SDL2-2.0.14.tar.gz                              解压   
$ apt --fix-broken install        
$ cd SDL2-2.0.14/
$ ./configure --prefix=$PWD/_install                       配置安装环境
```
做到上一步的时候终端报了一个错
```
configure: error: in `/home/linux-myl/SDL2-2.0.14':
configure: error: C compiler cannot create executables
```
感觉是gcc的编译环境没有配置好，所以重新安装配置一下gcc
```
$ apt-get install gcc libc6-dev
```
在安装时又出现了如下的错误
```
libc6-dev : Depends: libc6 (= 2.23-0ubuntu11.3) but 2.27-3ubuntu1.2 is to be installed
```
在网上搜了一下，说是源的版本没有更新。尝试使用`sudo apt-get update`，发现已经是最新版本了。于是索性换源，原来我用的是清华源，换成了阿里源。阿里源的网址以及换源的过程见[这个博客](https://blog.csdn.net/qq_35451572/article/details/79516563)。
换源后重新安装gcc，重新执行`./configure --prefix=$PWD/_install`，发现没有抱错，安装环境配置OK。
**3. 安装**
```
$ make clean
$ make && make install
$ cd _install/
$ tree                 _install目录的树状结构

.
├── bin
│   └── sdl2-config
├── include
│   └── SDL2
│       ├── begin_code.h
│       ├── close_code.h
│       ├── SDL_assert.h
│       ├── SDL_atomic.h
│       ├── SDL_audio.h
│       ├── SDL_bits.h
│       ├── SDL_blendmode.h
│       ├── SDL_clipboard.h
│       ├── SDL_config.h
│       ├── SDL_cpuinfo.h
│       ├── SDL_egl.h
│       ├── SDL_endian.h
│       ├── SDL_error.h
│       ├── SDL_events.h
│       ├── SDL_filesystem.h
│       ├── SDL_gamecontroller.h
│       ├── SDL_gesture.h
│       ├── SDL.h
│       ├── SDL_haptic.h
│       ├── SDL_hints.h
│       ├── SDL_joystick.h
│       ├── SDL_keyboard.h
│       ├── SDL_keycode.h
│       ├── SDL_loadso.h
│       ├── SDL_locale.h
│       ├── SDL_log.h
│       ├── SDL_main.h
│       ├── SDL_messagebox.h
│       ├── SDL_metal.h
│       ├── SDL_misc.h
│       ├── SDL_mouse.h
│       ├── SDL_mutex.h
│       ├── SDL_name.h
│       ├── SDL_opengles2_gl2ext.h
│       ├── SDL_opengles2_gl2.h
│       ├── SDL_opengles2_gl2platform.h
│       ├── SDL_opengles2.h
│       ├── SDL_opengles2_khrplatform.h
│       ├── SDL_opengles.h
│       ├── SDL_opengl_glext.h
│       ├── SDL_opengl.h
│       ├── SDL_pixels.h
│       ├── SDL_platform.h
│       ├── SDL_power.h
│       ├── SDL_quit.h
│       ├── SDL_rect.h
│       ├── SDL_render.h
│       ├── SDL_revision.h
│       ├── SDL_rwops.h
│       ├── SDL_scancode.h
│       ├── SDL_sensor.h
│       ├── SDL_shape.h
│       ├── SDL_stdinc.h
│       ├── SDL_surface.h
│       ├── SDL_system.h
│       ├── SDL_syswm.h
│       ├── SDL_test_assert.h
│       ├── SDL_test_common.h
│       ├── SDL_test_compare.h
│       ├── SDL_test_crc32.h
│       ├── SDL_test_font.h
│       ├── SDL_test_fuzzer.h
│       ├── SDL_test.h
│       ├── SDL_test_harness.h
│       ├── SDL_test_images.h
│       ├── SDL_test_log.h
│       ├── SDL_test_md5.h
│       ├── SDL_test_memory.h
│       ├── SDL_test_random.h
│       ├── SDL_thread.h
│       ├── SDL_timer.h
│       ├── SDL_touch.h
│       ├── SDL_types.h
│       ├── SDL_version.h
│       ├── SDL_video.h
│       └── SDL_vulkan.h
├── lib
│   ├── cmake
│   │   └── SDL2
│   │       ├── sdl2-config.cmake
│   │       └── sdl2-config-version.cmake
│   ├── libSDL2-2.0.so.0 -> libSDL2-2.0.so.0.14.0
│   ├── libSDL2-2.0.so.0.14.0
│   ├── libSDL2.a
│   ├── libSDL2.la
│   ├── libSDL2main.a
│   ├── libSDL2main.la
│   ├── libSDL2.so -> libSDL2-2.0.so.0.14.0
│   ├── libSDL2_test.a
│   ├── libSDL2_test.la
│   └── pkgconfig
│       └── sdl2.pc
└── share
    └── aclocal
        └── sdl2.m4

9 directories, 90 files
```
**4. 系统LD_LIBRARY_PATH环境变量配置**
最后为了方便其他程序在链接时能够找到SDL2的库，需要将SDL2库的路径加入到系统的环境变量中。
```
$ export LD_LIBRARY_PATH=$LD_LIBRARY_PTAH:/home/linux-myl/SDL2-2.0.14/_install/lib
```
如果路径写错了重新添加时需要删除原先错误的路径，重置LD_LIBRARY_PATH。
```
$ unset LD_LIBRARY_PATH
```
还需要注意的是export这种方法在终端被关闭后，LD_LIBRARY_PATH会清除之前做的操作。所以下次打开终端时还得重新往环境变量放入SDL2库的路径。
至此，SDL2就安装好了，后续调用时只需要在Makefile中指明SDL2安装时_install文件夹下的include和lib位置就可以使用SDL2的函数功能啦！！

> 来自: [Ubuntu下安装SDL2（记录一下自己的踩的坑）_ubuntu sdl2-CSDN博客](https://blog.csdn.net/qq_40017011/article/details/119748492)


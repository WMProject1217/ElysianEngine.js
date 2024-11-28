# ElysianEngine.js

A GalGame Scripting Engine on pure JavaScript

ElysianEngine.js 是一种基于纯JavaScript环境的GalGame脚本引擎，在Chromium 64或更新版本上工作。

可以在 https://wmproject1217.github.io/ElysianEngine/test.html 查看示例。

## 依赖

必须 wmcomlib.js/wmassetsloader.js (0.1.3或更新版本)

可选 wmmidisynth.js/wmmidisynth.js (0.2.0或更新版本) 用于提供基于函数发生的MIDI音频播放支持

## 使用说明

*//注意: 这些内容随版本更新可能变化*

*//注意: 最好在HTTP(s)或chrome-extension协议上运行*

### 加载一个已经存在的脚本

`
(async () => {

            await window.ElysianEngine._init_({
                script_chroot: "./assets/rcdata/",
                //(string) 脚本资源文件的根目录
                script_onerrorresumenext: 0,
                //(int number) 当发生错误时继续运行
                script_ticktimeout: 1,
                //(int number) 每刻之间的额外延迟
                save_disablecookie: 1,
                //(int number) (保留以做将来使用) 不使用cookie保存数据
                save_nodemodechroot: "./saves/",
                //(string) (保留以做将来使用) 在Node.js环境中的存档目录
                audio_voiceplaymode: 0,
                //(int number) 语音播放模式。设为0时，播放新的语音时将暂停旧的语音
                audio_voicevolume: 1,
                //(float number) 语音音量。在区间[0,1]中
                audio_bgmvolume: 1,
                //(float number) BGM音量。在区间[0,1]中
                audio_bgsvolume: 1
                //(float number) BGS音量。在区间[0,1]中
            });
            await window.ElysianEngine.loadscript("./assets/ElysiaDX.wmgesh");
            //加载并执行特定的脚本
            
        })();
`

### 编写一个脚本

ElysianEngine.js作为一个刚刚始露苗头的工程，没有很多现成的示例以供参考。本节帮助你从零开始编写一个适用于ElysianEngine.js的脚本。

#### 编写Ciallo,World!脚本

你需要配置一个网站，这可以通过nginx等应用程序完成。本工程仅需要静态文件，无需配置php等动态页面框架。

请将本工程以及 wmassetsloader.js 放置到网站根目录\lib\wmcomlib.js\下

打开VSCode，新建文件，编码UTF-8，输入以下内容:

`
!#wmgesh-1.0

\[charname\]Ciallo,World!
`

保存为ciallo.wmscript，放置到网站根目录\assets\下。

编写内容如下，编码为UTF-8的index.html，放置到网站根目录下。

`
\<!DOCTYPE html>

\<html>

\<head>

    <meta charset="utf-8" />
    <title>Elysian Engine</title>
    <link rel="stylesheet" href="./lib/wmcomlib.js/ElysianEngine.css">
    <script src="./lib/wmcomlib.js/wmassetsloader.js"></script>
    <script src="./lib/wmcomlib.js/ElysianEngine.js"></script>
    
\</head>

\<body>

    <script>
        async function init() {
            await window.ElysianEngine._init_({
                script_chroot: "./assets/rcdata/",
                script_onerrorresumenext: 0,
                script_ticktimeout: 1,
                save_disablecookie: 1,
                save_nodemodechroot: "./saves/",
                audio_voiceplaymode: 0,
                audio_voicevolume: 1,
                audio_bgmvolume: 1,
                audio_bgsvolume: 1
            });
            await window.ElysianEngine.loadscript("./assets/ciallo.wmgesh");
        }
        setTimeout(() => {
            init();
        }, 1000);
    </script>
    
\</body>

\</html>
`

在Chromium 64或更新版本的浏览器上访问你的网站，等待几秒，屏幕上将出现包含"Ciallo,World!"字段的对话框。

#### 在屏幕上显示选项

`
[]请选择

start menu

0    哼哼，哼

1    喵~

end menu

choose valdx

goto ext_${valdx}

:ext_0

[]啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊

exit

:ext_1

[]摸摸猫娘

exit
`

当用户选择后，选项的值会被附到变量上，可以使用这种方式完成一些跳转。

#### set

`
set asct=1
`

设置变量，以上行将变量 asct 的值设为 1。

#### sleep

`
sleep 1000
`

以上行会使脚本暂停 1000 毫秒。

#### 音频有关接口

`
audio_bgmplay audio/audio.wav
\## 播放BGM，该路径前会自动加上 script_chroot 的值

audio_bgmpause
\## 暂停BGM

audio_bgmresume
\## 从暂停的地方继续播放BGM

audio_bgmhalt
\## 终止BGM

audio_bgsplay 
\## 播放BGS，该路径前会自动加上 script_chroot 的值

audio_voiceplay 
\## 播放语音，该路径前会自动加上 script_chroot 的值

audio_voicepause
\## 仅在 audio_voiceplaymode 为 0 时有效，暂停语音

audio_voiceresume
\## 仅在 audio_voiceplaymode 为 0 时有效，从暂停的地方继续播放语音

audio_voicehalt
\## 仅在 audio_voiceplaymode 为 0 时有效，终止语音
`
#### js

`
js alert("Ciallo,World!")
`

执行JavaScript行，仅在ElysianEngine.js中有效。

#### exit

`
exit
`

退出脚本执行。

#### FAQ

##### 为何不创建虚拟机？

在 ElysianEngine.js 1.4.* 版本时，脚本曾在虚拟机中工作。由于性能问题和脚本的简单性要求，ElysianEngine 中的脚本不在虚拟机中运行。

##### 为什么叫 ElysianEngine.js？

最早，本工程作为 wmshadow.js 的一部分，文件名为 wmge.js 和 wmge.css。在 wmge-1.3.0 附近，wmshadow.js 解体，本工程放到 wmcomlib.js 目录下，由于名称缺乏辨识性，而我又正巧在崩崩崩里过乐土剧情，逐命名为 ElysianEngine.js。

##### 本工程是否有原生win32版本？

本工程有原生win32版本，但因性能、兼容性等问题暂未发布。本工程最初在 VB6 上实现，后来逐渐进化为 VisualFreeBasic 分支、JavaScript 分支和 C++ 分支。为保证脚本兼容性，优先开发了 JavaScript 分支。其他分支可能在几年内跟上。

##### 为什么教程写的乱七八糟的？

对不起，我只是一只猫娘。喵~


---
title: 笔记本电脑一键关闭显示屏
layout: post
mathjax: false
author: Meyer
catalog: true
header-img: "img/notebook.jpg"
tags: 
    - Windows
    - 技巧

---

👉在[CSDN](https://blog.csdn.net/weixin_42368779/article/details/109923592)中查看


新买的笔记本电脑没有一键关闭显示屏的功能键，只有调节亮度的功能键。虽然可以手动将亮度调到最暗来关闭显示屏，但是往往需要要按很多下，亮屏也需要重新调节亮度，特别麻烦。  


参考了这篇[文章](https://www.makeuseof.com/tag/3-quickest-ways-turn-computer-screen-windows/)的方法，提供两种解决方案，一是修改电源键的功能，二是创建一个bat脚本来关闭显示器，使用第二种方式我们之后还可以为其设置快捷键。

## 修改电源键的功能
进入控制面板 > 硬件和声音 > 电源选项，左侧点击“选择电源按钮的功能”，相应地将“按电源按钮时”对应的选项改为“关闭显示器”即可。
<br>
![修改电源键功能](https://img-blog.csdnimg.cn/20201122112714810.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjM2ODc3OQ==,size_16,color_FFFFFF,t_70#pic_center)
<br>
这种方法方便快捷，简单有效。注：电源键开机、强制关机等功能不会受影响。
<br>
## 使用bat脚本

若不想修改电源键的功能，可尝试以下方法。创建一个off.bat文件，输入以下内容
 

```bash
powershell (Add-Type '[DllImport(\"user32.dll\")]^public static extern int SendMessage(int hWnd, int hMsg, int wParam, int lParam);' -Name a -Pas)::SendMessage(-1,0x0112,0xF170,2)
```


双击运行即可关闭屏幕，但是每次运行都会弹出cmd窗口，很不美观。为了每次运行时不弹出cmd窗口，创建一个off.vbs文件，输入以下内容，将其和off.bat放至同一文件夹下。

```bash
Set ws=WScript.CreateObject("WScript.Shell")
ws.Run "off.bat",0
```

双击off.vbs，即可运行批处理文件，且没有cmd窗口弹出。我们还可以发送快捷方式到桌面，在“属性"中设置快捷键，这样就能通过快捷键来关闭显示屏。注：快捷方式须放在桌面，否则快捷键不起作用。

![设置快捷键](https://img-blog.csdnimg.cn/20201122115054671.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjM2ODc3OQ==,size_16,color_FFFFFF,t_70#pic_center)
<br>
这个方法虽然可以自定义快捷键关闭显示屏，但是实测响应较慢，有时甚至需要几秒钟。大家可以根据自己的喜好决定使用哪种方法，当然两个方法可以同时设置，若有更好的解决办法欢迎留言。  


**参考**
1. [The Quickest Ways to Turn Your Screen Off in Windows](https://www.makeuseof.com/tag/3-quickest-ways-turn-computer-screen-windows/)
2. [脚本Turn off screen](https://gallery.technet.microsoft.com/scriptcenter/Turn-off-screen-4d173e0a)
3. [bat批处理文件运行时隐藏cmd窗口](https://blog.csdn.net/qwl755/article/details/86684534?utm_medium=distribute.pc_relevant.none-task-blog-baidulandingword-3&spm=1001.2101.3001.4242)
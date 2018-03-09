---
title: '[python实例] - 跳一跳'
date: 2018-03-02 17:54:31
tags:
  - python
---

> 祭出已经玩了很久的跳一跳, 就当是在python的路上回忆吧.

**技术方案**：通过adb连接android手机，获取屏幕信息[跳一跳]，再利用ginput获取起始点和目标位置，测试并统计出跳一跳按压时长和位移的关系值，通过adb命令模拟手指按压。
**注意事项**：为避免微信检测是否为脚本执行，swipe的按压位置要随机
**升级方案**：利用python的图像处理，自动获取起始点和目标位置，可以使得程序更加自动化。

```python
import subprocess
from PIL import Image
from pylab import *
import random

x = str(random.randint(300,700))
y = str(random.randint(1300,1800))

cmd = "adb shell screencap -p /sdcard/t1t.png && adb pull /sdcard/t1t.png"

p = subprocess.Popen(cmd, shell=True)
p.communicate()

img = array(Image.open('./t1t.png'))
imshow(img)

xy = ginput(2)
show()

def dist (l):
  return ((l[1][0]-l[0][0])**2 + (l[1][1]-l[0][1])**2)**0.5 * 1.4

l = dist(xy)
ll = "adb shell input swipe " + x + " " + y + " " + x + " " + y + " " + str(int(l))

subprocess.Popen(ll, shell=True)
```

此方法每跳一格都要执行一次命令，可以按升级方案处理。
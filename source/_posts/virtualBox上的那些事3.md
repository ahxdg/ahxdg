---
title: virtualBox上的那些事(三)
date: 2018-08-17 18:22:01
tags:
  - linux
---

> 这篇文章，我们聊一聊虚拟机的后台运行吧！

查了一下，virtualBox有个VBoxManage.exe，打开cmd，进入到virtualBox目录下
```
查看一下这个命令的参数： VBoxManage -h

VBoxManage [<general option>] <command>

General Options:

  [-v|--version]            print version number and exit
  [-q|--nologo]             suppress the logo
  [--settingspw <pw>]       provide the settings password
  [--settingspwfile <file>] provide a file containing the settings password
  [@<response-file>]        load arguments from the given response file (bourne style)

Commands:

  list [--long|-l] [--sorted|-s]          vms|runningvms|ostypes|hostdvds|hostfloppies|
                            intnets|bridgedifs|hostonlyifs|natnets|dhcpservers|
                            hostinfo|hostcpuids|hddbackends|hdds|dvds|floppies|
                            usbhost|usbfilters|systemproperties|extpacks|
                            groups|webcams|screenshotformats

  showvminfo                <uuid|vmname> [--details]
                            [--machinereadable]
  showvminfo                <uuid|vmname> --log <idx>

  registervm                <filename>

  unregistervm              <uuid|vmname> [--delete]

  createvm                  --name <name>
                            [--groups <group>, ...]
                            [--ostype <ostype>]
                            [--register]
                            [--basefolder <path>]
                            [--uuid <uuid>]
...

 startvm                   <uuid|vmname>...
                            [--type gui|sdl|headless|separate]
                            [-E|--putenv <NAME>[=<VALUE>]]
...
```

ok，我们查看一下有几台虚拟机：
```
VBoxManage list vms

"centos7" {abe0c1a1-da62-4016-b834-ec4f644eb1f6}
"cs2" {091d1b5b-372d-4dcb-a164-536b3054e016}
```

发现我装了两天虚拟机

上面有个startvm的命令，后面有几个参数：gui，sdl，headless，separate

用这条命令，就能后台运行了...

VBoxManage startvm cs2 --type headless

其他的命令，有兴趣就去试试吧。

最好，添加一条系统变量，这样就能全局使用VBoxManage了

^_^
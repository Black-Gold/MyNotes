---
title: Windows
description: Windows系统常用设置和软件等
icon: fontawesome/brands/windows
---

# Windows

## Windows常用快捷键参考

| 快捷键                            | 快捷键作用                                  |
| :------------------------------- | :----------------------------------------- |
| ==++windows+e++==                | :open_file_folder: 打开文件资源管理器        |
| ++windows+num0+tilde+num9++      | win键+数字1至9到0依次从左往右打开任务栏应用     |
| ==++windows+r++==                | :simple-windowsterminal: 打开Run对话框      |
| ==++windows+i++==                | :gear: 打开系统设置                          |
| ==++windows+l++==                | :material-monitor-account: 锁屏或切换账号    |
| ==++ctrl+windows+d++==           | :material-monitor-multiple: 创建新的虚拟桌面 |
| ==++ctrl+windows+arrow-left++==  | :material-eye-arrow-left: 向左切换虚拟桌面   |
| ==++ctrl+windows+arrow-right++== | :material-eye-arrow-right: 向右切换虚拟桌面  |
| ==++windows+shift+slash++==      | :material-help: 打开windows快捷显示帮助      |


=== "WSL"

    [下载WSL Linux发行版官方链接 :octicons-link-16: ](https://learn.microsoft.com/en-us/windows/wsl/install-manual#downloading-distributions){ .md-button }

    ```powershell title="自定义WSL发行版安装"
    Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux # (1)!

    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart # (2)!
    Invoke-WebRequest -Uri https://aka.ms/wslubuntu2004 -OutFile Ubuntu.appx -UseBasicParsing
    move .\Ubuntu.appx .\Ubuntu.zip
    Expand-Archive .\Ubuntu.zip # (3)!
    ```

    1. 查看`适用于Linux的Windows子系统`功能是否已经启用
    2. 启用`适用于Linux的Windows子系统`功能
    3. 需要注意的是部分发行版需要多次解压，解压后进入解压后的目录，执行对应版本的exe进行初始化！初始化完毕后，将exe对应的路径添加到Path中。


=== "docker-desktop"

    ```powershell title="自定义docker-desktop安装"
    # (1)!
    ./'Docker Desktop Installer.exe' install --installation-dir=D:\Docker --wsl-default-data-root=E:\dockerwsl --no-windows-containers --backend=wsl-2
    wsl -l -v # (2)!
    ```

    1. :fontawesome-brands-docker: 自定义docker-desktop安装和数据目录
    2. 查看当前wsl发行版状态

??? tip "更改远程桌面RDP端口"
    ==regedit打开注册表，分别更改其下的PortNumber键值==
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\Wds\rdpwd\Tds\tcp
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp


## 脚本相关

```batch title="windows自动关闭脚本"
sleep 3600; shutdown -s
rem or
at 00:00:00PM shutdown -s
rem or
schtasks /create /sc once /tn "auto shutdown my computer" /tr "shutdown -s" /st 15:30

rem Schedule daily shutdown
At 11:00:00PM /every:M,T,W,TH,F,SA,SU shutdown -s

rem Schedule automatic resart
at 11:00:00PM shutdown -r

rem For Sleep
sleep number_of_seconds_to_wait; shutdown -r
```

```batch title="RDP.bat"
rem bat脚本远程连接脚本
rem 使用示例：rdp.bat "my.host.name.de" "port" "username" "password"

:: RDP connection without password prompt ------------
:: %1 = hostname
:: %2 = port
:: %3 = username
:: %4 = password
:: ---------------------------------------------------
cmdkey /add:"%~1" /user:"%~3" /pass:"%~4"
start /wait mstsc /v:"%~1:%~2"
cmdkey /delete:"%~1"

rem 刷新组策略
gpupdate/force
gpupdate /target:computer
```

```batch title="DailyCleanup.bat" hl_lines="15-16 25-26"
@echo off&setlocal enabledelayedexpansion
rem 根据当前日期获取，年月日串
set yyyy=%date:~0,4%
set mm=%date:~5,2%
set day=%date:~8,2%
set "Ymd=%yyyy%-%mm%-%day%"

rem 把年月日串中末尾空格替换掉
set "Ymd=%Ymd: =%"
echo %Ymd%

rem 获取n天前的日期
for /f "tokens=1-3 delims=-:/ " %%a in ("%date%") do (set Y=%%a&set M=%%b&set D=%%c&if "!M:~0,1!"=="0" set M=!M:~1!
if "!D:~0,1!"=="0" set D=!D:~1!)
rem 想获取n天前，就修改set/a D-=n天数就行
set/a D-=1 & if !D! leq 0 (set/a M-=1 & if !M!==0 set/a Y-=1,M=12
set/a "T=^!(M-2)","R=(^!(Y%%4)&^!^!(Y%%100))|^!(Y%%400)","C=^!(M-4)|^!(M-6)|^!(M-9)|^!(M-11)","D=T*(28+R)+C*30+(^!T&^!C)*31"+D)
set M=0%M%&set D=0%D%
set previousdate=%Y%-%M:~-2%-%D:~-2%
echo %previousdate%

rem 删除前一天的压缩文件
del E:\compresed\%previousdate%.7z /q

rem 批处理命令延迟方法,此处延迟5秒
choice /t 5 /d y /n >nul

rem 压缩今天的csv文件
cd /d D:
cd D:\7-Zip
7z.exe a -r E:\compresed\%Ymd%.7z E:\log_%Ymd%.csv -m0=LZMA2 -mx=9 -aoa -y
```

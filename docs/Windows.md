# Windows

## 自动关机

```bat
sleep 3600; shutdown -s
或
at 00:00:00PM shutdown -s
或
schtasks /create /sc once /tn "auto shutdown my computer" /tr "shutdown -s" /st 15:30

* Schedule daily shutdown *

At 11:00:00PM /every:M,T,W,TH,F,SA,SU shutdown -s

* Schedule automatic resart *

at 11:00:00PM shutdown -r

* For Sleep *

sleep number_of_seconds_to_wait; shutdown -r

```

serial number for RAM, motherboard, hard disk,Changing Computer Name

```bat
wmic memorychip get serialnumber
wmic diskdrive get serialnumber
wmic baseboard get serialnumber
wmic cdrom where drive='d:' get SerialNumber

WMIC computersystem where caption='currentname' rename newname
```

Disable WiFi connection

```bat
netsh interface set interface name="Wireless Network Connection" admin=DISABLED
```

Enable/Disable Firewall

```bat
* For XP/Server 2003 *

netsh firewall set opmode mode=ENABLE
netsh firewall set opmode mode=DISABLE

* For later versions *

netsh advfirewall set currentprofile state on
netsh advfirewall set  currentprofile state off

netsh advfirewall set domainprofile state on
netsh advfirewall set domainprofile state off

netsh advfirewall set privateprofile state on
netsh advfirewall set privateprofile state off

netsh advfirewall set publicprofile state on
netsh advfirewall set publicprofile state off

netsh advfirewall set  allprofiles state on
netsh advfirewall set  allprofiles state off
```

Join or remove a Computer to Domain

```bat
netdom.exe join %computername% /domain:DomainName /UserD:DomainName\UserName /PasswordD:Password

netdom.exe remove %computername% /domain:Domainname /UserD:DomainName\UserName /PasswordD:Password
```

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
set/a D-=1&if !D! leq 0 (set/a M-=1&if !M!==0 set/a Y-=1,M=12
set/a "T=^!(M-2)","R=(^!(Y%%4)&^!^!(Y%%100))|^!(Y%%400)","C=^!(M-4)|^!(M-6)|^!(M-9)|^!(M-11)","D=T*(28+R)+C*30+(^!T&^!C)*31"+D)
set M=0%M%&set D=0%D%
set previousdate=%Y%-%M:~-2%-%D:~-2%
echo %previousdate%

rem 删除前一天的压缩文件
del E:\compresed\%previousdate%.7z /q

rem 压缩今天的日志文件
cd /d D:
cd D:\7-Zip
7z.exe a -r E:\compresed\%Ymd%.7z E:\log_%Ymd%.csv -m0=LZMA2 -mx=9 -aoa -y

java 环境

变量名：Path
变量值：%java_home%\bin
新建变量名：JAVA_HOME
变量值：D:\jdk1.8.0_111（这里是jdk的安装目录）
新建变量名：CLASSPATH
变量值：.;%JAVA_HOME%\lib

```txt
VMware Workstation 15.x
FU512-2DG1H-M85QZ-U7Z5T-PY8ZD
CU3MA-2LG1N-48EGQ-9GNGZ-QG0UD
GV7N2-DQZ00-4897Y-27ZNX-NV0TD
YZ718-4REEQ-08DHQ-JNYQC-ZQRD0
GZ3N0-6CX0L-H80UP-FPM59-NKAD4
YY31H-6EYEJ-480VZ-VXXZC-QF2E0
ZG51K-25FE1-H81ZP-95XGT-WV2C0
VG30H-2AX11-H88FQ-CQXGZ-M6AY4
CU7J2-4KG8J-489TY-X6XGX-MAUX2
FY780-64E90-0845Z-1DWQ9-XPRC0
UF312-07W82-H89XZ-7FPGE-XUH80
AA3DH-0PYD1-0803P-X4Z7V-PGHR4

visio密钥
W9WC2-JN9W2-H4CBV-24QR7-M4HB8

```

## windows更改远程桌面端口

```bat
regedit打开注册表，分别更改其下的PortNumber键值
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\Wds\rdpwd\Tds\tcp
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp

bat脚本远程连接；脚本如下。使用示例：rdp.bat "my.host.name.de" "port" "username" "password"

:: RDP connection without password prompt ------------
:: %1 = hostname
:: %2 = port
:: %3 = username
:: %4 = password
:: ---------------------------------------------------
cmdkey /add:"%~1" /user:"%~3" /pass:"%~4"
start /wait mstsc /v:"%~1:%~2"
cmdkey /delete:"%~1"

:: 刷新组策略
gpupdate/force
gpupdate /target:computer

```

windows服务绑定IP问题
netsh-->>http-->>show iplisten
delete iplisten ipaddress=x.x.x.x

网易云：[27493175.mp3]
nslookup -vc github.com 8.8.8.8

choice /t 5 /d y /n >nul    # 批处理命令延迟方法

https://www.jsdelivr.com/package/gh/Black-Gold/MyNotes

## Windows Commands详解大全

远程桌面服务有2种工作模式，Administration Mode和Application Mode。这两种工作模式分别适应不同的场景需求

Administration Mode
默认的Windows Server安装后就处于该模式，服务器不要求客户端提供License，但是仅仅允许2个远程桌面连接

Application Mode
微软的Windows Server为了满足用户复杂的远程连接的场景需要，提供了类似于Citrix基于远程桌面的解决方案，用户在安装远程桌面会话服务(Remote Desktop  Session Host)后，服务器转换为application mode，才可以不限制用户数进行连接

此时，服务器默认有120天的Grace Period，允许不限制用户数的远程连接。在120天后，您必须指定一台已经安装Remote Desktop License的远程桌面授权服务器才可以多用户继续连接，否则会提示“由于没有远程桌面授权服务器可以提供许可证，远程会话被中断“的报错。但是此时您还可以通过mstsc /admin命令连接到远程桌面

1.纯cmdlet命令
Get-Process -Name notepad | Stop-Process

2.cmdlet+遍历
Get-Process -Name notepad | foreach-object{$_.Kill()}

3.WMI 对象 + 遍历 + 对象方法
Get-WmiObject Win32_Process -Filter "name = 'notepad.exe'" | ForEach-Object{$_.Terminate()  | Out-Null }

4.WMI 对象 + 遍历 + cmdlet方法
Get-WmiObject Win32_Process -Filter "name = 'notepad.exe'" | Invoke-WmiMethod -Name Terminate | Out-Null

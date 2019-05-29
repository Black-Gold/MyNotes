# Raspberrypi

```sh
关于HDMI设置
/opt/vc/bin/tvservice
tvservice [OPTION]...
  -p, --preferred                   Power on HDMI with preferred settings
  -e, --explicit="GROUP MODE DRIVE" Power on HDMI with explicit GROUP (CEA, DMT, CEA_3D_SBS, CEA_3D_TB, CEA_3D_FP, CEA_3D_FS)
                                      MODE (see --modes) and DRIVE (HDMI, DVI)
  -t, --ntsc                        Use NTSC frequency for HDMI mode (e.g. 59.94Hz rather than 60Hz)
  -c, --sdtvon="MODE ASPECT [P]"    Power on SDTV with MODE (PAL or NTSC) and ASPECT (4:3 14:9 or 16:9) Add P for progressive
  -o, --off                         Power off the display
  -m, --modes=GROUP                 Get supported modes for GROUP (CEA, DMT)
  -M, --monitor                     Monitor HDMI events
  -s, --status                      Get HDMI status
  -a, --audio                       Get supported audio information
  -d, --dumpedid <filename>         Dump EDID information to file
  -j, --json                        Use JSON format for --modes output
  -n, --name                        Print the device ID from EDID


查找当前树莓派CPU模块名
cat /lib/modules/$(uname -r)/modules.builtin | grep wdt

还可以设置内存耗尽就重启，如min-memory =1 前的注释#号去掉
还可以设置监控的间隔，如 interval = 1 前的注释#号去掉，该1为任意数字，单位是秒，默认是10秒一次健康检查

/etc/init.d/watchdog start


chkconfig watchdog on

/etc/sysctl.conf
kernel.panic = 10

sysctl -P


1、通过shell编程获得cup温度值



进入树莓派中断控制台，依次输入以下指令获取实时温度值：

#进入根目录

   cd /

#读取temp文件，获得温度值

   cat sys/class/thermal/thermal_zone0/temp

#系统返回实时值

   40622


[说明]

      1）通过cat命令读取存放在sys/class/thermal/thermal_zone0目录下的温度文件temp获得返回值

      2）返回值为一个5位数的数值，实际温度为将该值除以1000即可！单位为摄氏度！


2、通过C语言编程获得cpu温度值

#选定一个目录，并在目录中创建cpu_temp.c文件，将以下代码输入：




#include <stdio.h>  
#include <stdlib.h>

//导入文件控制函数库  
#include <sys/types.h>  
#include <sys/stat.h>  
#include <fcntl.h>  
  
#define TEMP_PATH "/sys/class/thermal/thermal_zone0/temp"  
#define MAX_SIZE 20  
int main(void)
{  
    int fd;  
    double temp = 0;  
    char buf[MAX_SIZE];  

    // 以只读方式打开/sys/class/thermal/thermal_zone0/temp  
    fd = open(TEMP_PATH, O_RDONLY);

        //判断文件是否正常被打开
    if (fd < 0)
{  
        fprintf(stderr, "failed to open thermal_zone0/temp\n");  
        return -1;  
    }  

    // 读取内容  
    if (read(fd, buf, MAX_SIZE) < 0)
    {  
        fprintf(stderr, "failed to read temp\n");  
        return -1;  
    }  

    // 转换为浮点数打印  
    temp = atoi(buf) / 1000.0;  
    printf("temp: %.3f\n", temp);  

    // 关闭文件  
    close(fd);  
}

#编译C代码，输入以下指令：

  gcc -o cpu_temp cpu_temp.c

#运行程序

  ./cpu_temp

#系统返回实时值

  temp : 40.622

程序解读：

  1)关于open()、read()、close()函数使用，可看：【fcntl.h函数库的常用函数使用】。

  2)atoi(buf)函数是将buf中的字符串数据转换层整形数。

  3)gcc -o cpu_temp cpu_temp.c ：gcc为编译器、 -o参数表示将cpu_temp.c文件编译成可执行文件并存放到 cpu_temp文件夹中。

2、通过python语言编程获得cpu温度值

#选定一个目录，并在目录中创建cpu_temp.py文件，将以下代码输入：


# -*- coding: utf-8 -*-  
# 打开文件  
file = open("/sys/class/thermal/thermal_zone0/temp")  
# 读取结果，并转换为浮点数  
temp = float(file.read()) / 1000  
# 关闭文件  
file.close()  
# 向终端控制台打印  
print "temp : %.3f" %temp


#执行脚本

  Python cpu_temp.py

cpu温度获取这样写会不会更轻巧些
def getCPUtemperature():
return os.popen( ‘/opt/vc/bin/vcgencmd measure_temp’ ).read()[5:9]
或者直接读取系统自己的状态更新，省去模拟shell
def getCPUtemperature():
with open( “/sys/class/thermal/thermal_zone0/temp” ) as tempFile:
res = tempFile.read()
res=str(float(res)/1000)
return res

同理，getRaminfo()可以写成： return os.popen(‘free|tail -n +2’).readline().split()[1:4]
getDiskSpace()可以写成：return os.popen(‘df -h /|tail -n +2’).readline().split()[1:5]

popen的效率不高，但是配合shell相当灵活～～

```

那一多噶一尼
罗库瓦莫嘿多里得
那给丝特那勒达
阿KI卡母 诺约达
达噶一诺苏 给得喔
西里 祖祖吗得噶
阿一牙拉吧 一所可瓦里 勒母罗卡色卡一噶哦瓦鲁吗得瓦
汗那勒路 果多莫赖
所拉噶得一带
又古色乌诺哟鲁多
莫多拉拉嘿多KI拉给噶
哪zai噶噶牙一得瓦
牙祖里KI达 可可罗 马得莫 果瓦祖
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
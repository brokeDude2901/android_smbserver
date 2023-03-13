# android_smbserver
Use your Android into as SMB Server

# SMB Pros:
- Free
- Can mount as a native Windows Network Drive
- Can directly run exe as administrator on it without problems
# SMB Cons:
- Very slow when transfering lots of small files

## Step 1: Setup Termux on your Android device
Download and install the lastest APK release of Termux from https://github.com/termux/termux-app/releases
'Aquire Wakelock' and enable 'Keep Screen On' for faster performance
## Step 2: Setup SMB on Termux
Based on this Reddit thread: https://www.reddit.com/r/termux/comments/qb9ybp/using_termux_as_samba_server/
### Install samba
```bash
termux-setup-storage
pkg install samba
cp /data/data/com.termux/files/usr/share/doc/samba/smb.conf.example $PREFIX/etc/smb.conf
smbd -D -s $PREFIX/etc/smb.conf
```
### Set up a new user 'samba'
```bash
smbpasswd -L -a samba
```
### Manually edit your smb.conf, for example I mount only my phone Internal Storage as 'Internal' like this
```
[internal]
   comment = Internal storage
   path = /data/data/com.termux/files/home/storage/shared
   vfs objects = aio_pthread
   aio_pthread:aio open = yes
   read only = no
   browseable = no
   writable = yes
   guest ok = no
```
## Step 3: Setup loopback adapter on your Windows PC
Based on this answer on StackOverFlow: https://unix.stackexchange.com/a/723747 (https://gist.github.com/Hashbrown777/081e57ff9673a1f457e1c3a71b55cfaf#file-attachsmb-ps1)
### Install LoopbackAdapter and download the script
Open a new Terminal/Powershell as Admin
```powershell
iwr 'https://gist.githubusercontent.com/Hashbrown777/081e57ff9673a1f457e1c3a71b55cfaf/raw/28dcd74be1281812e2ef17b1bd36c6b5db216ca3/attachsmb.ps1'  -OutFile attachsmb.ps1
Install-Module -Name LoopbackAdapter -MinimumVersion 1.2.0.0
```
### Change '192.168.1.14' to your actual Android IP
```powershell
. ./attachsmb.ps1
Create-Host -Name androidsmb -Ip 10.254.0.1 -Dest 192.168.1.14 -Port 1139
```
### Reboot, right click on This PC and Map Network Drive: \\androidsmb\internal, enter your Samba username and password
![image](https://user-images.githubusercontent.com/46110534/224823235-938b253d-fb55-4ed8-b926-5dcea077951d.png)

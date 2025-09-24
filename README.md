# Windows-Install-WSL-and-Mount-Ext-File-System-Disk

[![借助WSL讀寫Ext4檔安系統](https://github.com/TechTutoPPT/Windows-Install-WSL-and-Mount-Ext-File-System-Disk/blob/main/cover.PNG)](https://youtu.be/Iar45xBuMzo)

由於Windows預設是不支持Ext4檔案系統的, 而Linux裝置又大多以Ext4作為檔案系統, 例如上期影片中小米路使用的USB磁碟格式便是Ext4,
那假如想在Windows中透過USB直接讀寫小米路由的USB磁碟可以怎樣辦呢? 其實方法有很多, 例如安裝Ext2Fsd以支援Ext4驅動, 
亦可使用如DiskGenius這類磁碟管理工具亦可, 而今集介紹的是直接以Windows 11 Pro內置支援的WSL系統來處理.
首先我們按Win+R, 輸入powershell(Ctrl+Shift+Enter以系統管員執行), 再執行以下指令安裝WSL:
```
wsl --install
```
然後更新WSL:
```
wsl --update
```
再來確認版本是否為WSL2:
```
wsl --list --verbose
```
確認後便可重啟WSL完成安裝:
```
wsl --shutdown
```

然後將Ext4檔案系統的USB磁碟連接電腦, 再於PowerShell中執行以下指令去查詢它的裝置ID(找出對應結果, 例如\\.\PHYSICALDRIVE2):
```
GET-CimInstance -query "SELECT * from Win32_DiskDrive"
```
然後使用 WSL掛載磁碟的命令去將該磁碟某分區掛載到Ubuntu指定的掛載點上, 例如/mnt/wsl/PHYSICALDRIVE2p1：
```
wsl --mount \\.\PHYSICALDRIVE2 --partition 1 --type ext4
```
成功後除了於Ubuntu內能以cd方式進入/mnt/wsl/PHYSICALDRIVE2p1管理檔案外, 亦可直接於Windows的檔案總管左下方找到Linux圖示進入上述位置去管理檔案.

另外假如想以Ubuntu深度管理該USB磁碟, 例如分割分區, 格式化磁碟這類操作, 則可執行以下指令將整顆磁碟掛載到Ubuntu的/dev下, 例如:
```
wsl --mount \\.\PHYSICALDRIVE2 --bare
```
然後於Ubuntu終端中執行lsblk或fdisk -l去被識別掛載的磁碟名稱, 例如/dev/sdc

最後安全卸載USB磁碟亦很重要, 請按以下先後步驟執行卸載操作:
先卸載Ubuntu內的掛載點,例如:
```
sudo umount /mnt/wsl/PHYSICALDRIVE2p1
```
再於PowerShell執行:
```
wsl --unmount \\.\PHYSICALDRIVE2
```
最後再到Windows檔案總管內將USB磁碟退出才駁走該碟.

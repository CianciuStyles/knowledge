# Windows

## Notes

### Registry

* The Windows Registry is a collection of databases that contains the system's configuration data.
* A Registry Hive is a group of Keys, subkeys, and values stored in a single file on the disk.
* The registry on any Windows system contains the following five root keys:
  * HKEY\_CURRENT\_USER: Contains the root of the configuration information for the user who is currently logged on.
  * HKEY\_USERS: Contains all the actively loaded user profiles on the computer.
  * HKEY\_LOCAL\_MACHINE: Contains configuration information particular to the computer (for any user).
  * HKEY\_CLASSES\_ROOT: The information that is stored here makes sure that the correct program opens when you open a file by using Windows Explorer.
  * HKEY\_CURRENT\_CONFIG: Contains information about the hardware profile that is used by the local computer at system startup.
* The majority of the hives are located in the `C:\Windows\System32\Config` directory and are:
  * `DEFAULT` (mounted on HKEY\_USERS\DEFAULT)
  * `SAM` (mounted on HKEY\_LOCAL\_MACHINE\SAM)
  * `SECURITY` (mounted on HKEY\_LOCAL\_MACHINE\Security)
  * `SOFTWARE` (mounted on HKEY\_LOCAL\_MACHINE\Software)
  * `SYSTEM` (mounted on HKEY\_LOCAL\_MACHINE\System)
* The other hives that contain user information are located in:
  * `C:\Users<username>\NTUSER.DAT` (mounted on HKEY\_CURRENT\_USER when a user logs in)
  * `C:\Users<username>\AppData\Local\Microsoft\Windows\USRCLASS.DAT` (mounted on HKEY\_CURRENT\_USER\Software\CLASSES)
* Another very important hive is called the AmCache hive, which is located in `C:\Windows\AppCompat\Programs\Amcache.hve` and contains information on programs that were recently run on the system.
* The transaction logs can be considered as the journal of the changelog of the registry hive, which means that the transaction logs can often have the latest changes in the registry that haven't made their way to the registry hives themselves. The transaction log for each hive is stored as a .LOG file in the same directory as the hive itself. It has the same name as the registry hive, but the extension is .LOG.
* Registry backups are the opposite of Transaction logs. These are the backups of the registry hives located in the `C:\Windows\System32\Config directory`. These hives are copied to the `C:\Windows\System32\Config\RegBack` directory every ten days.

#### Useful registry keys

* `NTUSER.DAT\Software\Microsoft\Office\VERSION` - Office recent files
* `NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs` - Recent files
* `SAM\Domains\Account\Users` - Users
* `SOFTWARE\Microsoft\Windows\CurrentVersion\Run` - Autoruns
* `SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce` - Autoruns
* `SOFTWARE\Microsoft\Windows NT\CurrentVersion` - OS version
* `SOFTWARE\Microsoft\Windows Portable Devices\Devices` - USB device volume names
* `SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName` - Computer name
* `SYSTEM\CurrentControlSet\Enum\USB` - USB devices information
* `SYSTEM\CurrentControlSet\Enum\USBSTOR` - USB devices information
* `SYSTEM\CurrentControlSet\Control\TimeZoneInformation` - Time zone information
* `SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces` - Network interfaces

## Resources

### Articles

* [Top Five Ways I Got Domain Admin on Your Internal Network before Lunch (2018 Edition)](https://adam-toscher.medium.com/top-five-ways-i-got-domain-admin-on-your-internal-network-before-lunch-2018-edition-82259ab73aaa) - Adam Toscher
* [Unofficial Guide to Mimikatz & Command Reference](https://adsecurity.org/?page\_id=1821) - Active Directory Security
* [Windows Privilege Escalation Fundamentals](https://fuzzysecurity.com/tutorials/16.html) - Ruben Boonen, Fuzzy Security

### GitHub repositories

* [Cain & Abel ](https://github.com/xchwarze/Cain)- password recovery tool for Microsoft Operating Systems
* [enum4Linux](https://github.com/CiscoCXSecurity/enum4linux) - tool for enumerating data from Windows and Samba hosts
* [enum4linux-ng](https://github.com/cddmp/enum4linux-ng) - A next generation version of enum4linux
* [Evil-WinRM](https://github.com/Hackplayers/evil-winrm) - The ultimate WinRM shell for hacking/pentesting
* [kerbrute](https://github.com/ropnop/kerbrute) - tool to perform Kerberos pre-auth bruteforcing
* [mimikatz](https://github.com/gentilkiwi/mimikatz) - A little tool to play with Windows security
* [RegRipper3.0](https://github.com/keydet89/RegRipper3.0)

### Tools

* [Registry Explorer](https://ericzimmerman.github.io/#!index.md) - Eric Zimmerman

### TryHackMe Rooms

* [Active Directory Basics](https://tryhackme.com/r/room/winadbasics)
* [Attacktive Directory](https://tryhackme.com/r/room/attacktivedirectory)
* [Blaster](https://tryhackme.com/r/room/blaster)
* [Ice](https://tryhackme.com/r/room/ice)
* [Windows Forensics 1](https://tryhackme.com/r/room/windowsforensics1)
* [Windows Fundamentals 1](https://tryhackme.com/jr/windowsfundamentals1xbx)
* [Windows Fundamentals 2](https://tryhackme.com/r/room/windowsfundamentals2x0x)
* [Windows Fundamentals 3](https://tryhackme.com/r/room/windowsfundamentals3xzx)

### Websites

* [Active Directory Security](https://adsecurity.org/)
* Windows Virtual Machines
  * [Windows 10 Enterprise](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-10-enterprise)
  * [Windows 11 developer environment](https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/)
  * [Windows 11 Enterprise](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-11-enterprise)

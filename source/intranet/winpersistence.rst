Windows持久化
========================================

隐藏文件
----------------------------------------
- 创建系统隐藏文件
    - ``attrib +s +a +r +h filename`` / ``attrib +s +h filename``
- 利用NTFS ADS (Alternate　Data　Streams) 创建隐藏文件
- 利用Windows保留字
    - ``aux|prn|con|nul|com1|com2|com3|com4|com5|com6|com7|com8|com9|lpt1|lpt2|lpt3|lpt4|lpt5|lpt6|lpt7|lpt8|lpt9``

权限提升
----------------------------------------
权限提升有多重方式，有利用二进制漏洞、逻辑漏洞等技巧。利用二进制漏洞获取权限的方式是利用运行在内核态中的漏洞来执行代码。比如内核、驱动中的UAF或者其他类似的漏洞，以获得较高的权限。

逻辑漏洞主要是利用系统的一些逻辑存在问题的机制，比如有些文件夹用户可以写入，但是会以管理员权限启动。

任意写文件利用
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
在Windows中用户可以写的敏感位置主要有以下这些

+ 用户自身的文件和目录，包括 ``AppData`` ``Temp``
+ ``C:\`` ，默认情况下用户可以写入
+ ``C:\ProgramData`` 的子目录，默认情况下用户可以创建文件夹、写入文件
+ ``C:\Windows\Temp`` 的子目录，默认情况下用户可以创建文件夹、写入文件

具体的ACL信息可用AccessChk, 或者PowerShell的 ``Get-Acl`` 命令查看。

可以利用对这些文件夹及其子目录的写权限，写入一些可能会被加载的dll，利用dll的加载执行来获取权限。

MOF
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
MOF是Windows系统的一个文件（ ``c:/windows/system32/wbem/mof/nullevt.mof`` ）叫做"托管对象格式"，其作用是每隔五秒就会去监控进程创建和死亡。

当拥有文件上传的权限但是没有Shell时，可以上传定制的mof文件至相应的位置，一定时间后这个mof就会被执行。

一般会采用在mof中加入一段添加管理员用户的命令的vbs脚本，当执行后就拥有了新的管理员账户。

sethc
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
``sethc.exe`` 是 Windows系统在用户按下五次shift后调用的粘滞键处理程序，当有写文件但是没有执行权限时，可以通过替换 ``sethc.exe`` 的方式留下后门，在密码输入页面输入五次shift即可获得权限。

凭证窃取
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
- Windows本地密码散列导出工具
    - mimikatz
    - wce
    - gsecdump
    - copypwd
    - Pwdump
- Windows本地密码破解工具
    - L0phtCrack
    - SAMInside
    - Ophcrack
- 彩虹表破解
- 本机hash+明文抓取
- win8+win2012明文抓取
- ntds.dit的导出+QuarkPwDump读取分析
- vssown.vbs + libesedb + NtdsXtract
- ntdsdump
- 利用powershell(DSInternals)分析hash

其他
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
- 组策略首选项漏洞
- DLL劫持
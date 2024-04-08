DSM 7.2（64551）经验证依然有效，如第二步激活失败，请1.多试几次 2.清空cookie 3.更换浏览器。

---

1. 首先启用admin账户，没有修改密码的顺便修改一个密码
2. 复制好你的序列号备用（**重要，千万不要在执行完第3步才跑去重新登陆找序列号，会导致激活失败**）
3. 假设你没有更改过端口号，那就在浏览器上打开下面的链接

> https://**你的群晖内网IP**:5001/webapi/auth.cgi?api=SYNO.API.Auth&version=3&method=login&account=**admin**&passwd=**admin的密码**&format=cookie


>http://192.168.1.104:5000/webapi/auth.cgi?api=SYNO.API.Auth&version=3&method=login&account=admin&passwd=admin的密码&format=cookie


>http://192.168.1.100:5000/webapi/auth.cgi?api=SYNO.API.Auth&method=Login&version=1&account=admin&passwd=admin的密码&format=cookie

如果did和sid显示一串字符，success为true则开启成功

4.继续在浏览器打开第二条链接

> https://**你的群晖内网IP**:5001/webapi/auth.cgi?api=SYNO.ActiveBackup.Activation&method=set&version=1&activated=true&serial_number=**黑群晖序列号**

>http://192.168.1.104:5000/webapi/auth.cgi?api=SYNO.ActiveBackup.Activation&method=set&version=1&activated=true&serial_number=**黑群晖序列号**

>http://192.168.1.100:5000/webapi/entry.cgi?api=SYNO.ActiveBackup.Activation&method=set&version=1&activated=true&serial_number=**黑群晖序列号**

activated和success返回true则开启成功

5.不用admin账户的话记得回去关掉

---

补充一下激活ABB for Microsoft 365和ABB for Google Workspace的方法，第一步相同，替换第二步的网址：

ABB for Microsoft 365：

> [https://你的群晖内网IP:5001/webapi/entry.cgi?api=SYNO.ActiveBackupOffice365&method=set_activation&version=1&serial_number=黑群晖](https://link.zhihu.com/?target=https%3A//100.64.199.91%3A5001/webapi/entry.cgi%3Fapi%3DSYNO.ActiveBackupOffice365%26method%3Dset_activation%26version%3D1%26serial_number%3D2250T2REMQ5PS)序列号

ABB for Google Workspace：

> [https://](https://link.zhihu.com/?target=https%3A//address%3A5001/webapi/entry.cgi%3Fapi%3DSYNO.ActiveBackupGSuite%26method%3Dset_activation%26version%3D1%26serial_number%3D)[你的群晖内网IP](https://link.zhihu.com/?target=https%3A//100.64.199.91%3A5001/webapi/entry.cgi%3Fapi%3DSYNO.ActiveBackupOffice365%26method%3Dset_activation%26version%3D1%26serial_number%3D2250T2REMQ5PS)[:5001/webapi/entry.cgi?api=SYNO.ActiveBackupGSuite&method=set_activation&version=1&serial_number=](https://link.zhihu.com/?target=https%3A//address%3A5001/webapi/entry.cgi%3Fapi%3DSYNO.ActiveBackupGSuite%26method%3Dset_activation%26version%3D1%26serial_number%3D)[黑群晖](https://link.zhihu.com/?target=https%3A//100.64.199.91%3A5001/webapi/entry.cgi%3Fapi%3DSYNO.ActiveBackupOffice365%26method%3Dset_activation%26version%3D1%26serial_number%3D2250T2REMQ5PS)序列号
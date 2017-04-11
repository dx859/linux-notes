Linux账号与组群
---
### UID 与 GID

在Linux中，系统通过帐号与组群划分用户，通过帐号识别码：UID（属主User ID），GID（属组Group ID）来识别用户。
使用 `id username` 来查看用户的UID和GID

### /etc/passwd 档案结构

linux把用户帐号放到/etc/passwd文件中，如下面所见，各个部分以(:)分割

    root:x:0:0:root:/root:/bin/bash
    bin:x:1:1:bin:/bin:/sbin/nologin
    ...
    mysql:x:996:1000::/home/mysql:/bin/false
    daixi:x:1000:1001::/home/daixi:/bin/bash
   
其中从左到右分别为：

1. 帳號名稱
2. 密碼: 以x为占位符，详细资料在/etc/shadow中
3. UID
    > 注意：当UID为0时，表示系统管理员，及root帐号，当其他用户改为0时，表示也具有root权限
    > 1-999表示系统帐号，系统上启动的网络服务需要较小的权限去运作，因此不需要root去运行这些服务，而且无需登录
    > 1000-4294967295，一般使用着的帐号
4. GID
5. 使用者说明，这里什么都没有表示空
6. 家目录
7. Shell：如果禁止用户登录则可改成：/sbin/nologin

在/etc/group中放着用户组群档案，从左到右分别为：群组名称，群组密码，GID，此群组支持的帐号名称（新版Linux初始用户群不会放在第四栏位）

---------

帐号管理
---
### 新增移除用户：useradd , passwd ,usermod, userdel
**useradd:**
useradd [-u UID] [-g 初始群組] [-G 次要群組] [-mM] [-c 說明欄] [-d 家目錄絕對路徑] [-s shell] 使用者帳號名
選項與參數：
-u  ：後面接的是 UID ，是一組數字。直接指定一個特定的 UID 給這個帳號；
-g  ：後面接的那個群組名稱就是我們上面提到的 initial group 啦～
      該群組的 GID 會被放置到 /etc/passwd 的第四個欄位內。
-G  ：後面接的群組名稱則是這個帳號還可以加入的群組。
      這個選項與參數會修改 /etc/group 內的相關資料喔！
-M  ：強制！不要建立使用者家目錄！(系統帳號預設值)
-m  ：強制！要建立使用者家目錄！(一般帳號預設值)
-c  ：這個就是 /etc/passwd 的第五欄的說明內容啦～可以隨便我們設定的啦～
-d  ：指定某個目錄成為家目錄，而不要使用預設值。務必使用絕對路徑！
-r  ：建立一個系統的帳號，這個帳號的 UID 會有限制 (參考 /etc/login.defs)
-s  ：後面接一個 shell ，若沒有指定則預設是 /bin/bash 的啦～
-e  ：後面接一個日期，格式為『YYYY-MM-DD』此項目可寫入 shadow 第八欄位，
      亦即帳號失效日的設定項目囉；
-f  ：後面接 shadow 的第七欄位項目，指定密碼是否會失效。0為立刻失效，
      -1 為永遠不失效(密碼只會過期而強制於登入時重新設定而已。)
      
在 /etc/passwd 裡面建立一行與帳號相關的資料，包括建立 UID/GID/家目錄等；
在 /etc/shadow 裡面將此帳號的密碼相關參數填入，但是尚未有密碼；
在 /etc/group 裡面加入一個與帳號名稱一模一樣的群組名稱；
在 /home 底下建立一個與帳號同名的目錄作為使用者家目錄，且權限為 700
      
      [root@study ~]# useradd -D
      GROUP=100		<==預設的群組
      HOME=/home		<==預設的家目錄所在目錄
      INACTIVE=-1		<==密碼失效日，在 shadow 內的第 7 欄
      EXPIRE=			<==帳號失效日，在 shadow 內的第 8 欄
      SHELL=/bin/bash		<==預設的 shell
      SKEL=/etc/skel		<==使用者家目錄的內容資料參考目錄
      CREATE_MAIL_SPOOL=yes   <==是否主動幫使用者建立郵件信箱(mailbox)

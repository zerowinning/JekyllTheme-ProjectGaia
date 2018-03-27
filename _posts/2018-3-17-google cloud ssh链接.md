## 首先

1.  创建实例后设置当前用户的新密码

```
$ sudo passwd ${whoami} // 下面以 user 代替 ${whoami}
# 输入新密码

```

1.  随便设置下 root 的新密码

```
$ sudo passwd root
# 输入新密码

```

## 在本地生成私钥和公钥

```
$ cd ~/.ssh
$ ssh-keygen -f myKey
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): (给 private key 设置一个密码，避免私钥被人盗用的风险)
Enter same passphrase again: (再次输入上次相同密码)
Your identification has been saved in myKey.
Your public key has been saved in myKey.pub.
The key fingerprint is:
SHA256:EW7ow1wCKAh1rM/GG08ZAwOy+7+SUiT0rFXY2f8mNvk user@computer-name.local
The key's randomart image is:
+---[RSA 2048]----+
|=.o+= o .        |
|o+.o+= + .       |
|o.o..oo *        |
|..o+ +o+ o       |
|.oo+  =+S o      |
| o. * o. = o     |
| ..o =  . =      |
|. o.. .    E     |
| . .o.           |
+----[SHA256]-----+
# 此时会生成 公钥 myKey.pub 和 私钥 myKey

```

## 复制公钥

```
$ cat myKey.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCjHKPaeglRVJzAhNq+W
中间部分省略。。。
dKx8sJ0Rw4kUqm2eU2vo8S5IEA0Nk2f7BtVGE8VOCHgmDbv2tLp9845UVp1 user@computer-name.local
# 把这长长的一段复制下来，把其中的 user@computer-name.local 改为你在浏览器 SSH 登入之后的当前用户名 ${whoami}

```

## 导入公钥

```
# 进入谷歌云平台页面 -> 计算引擎 -> 元数据 -> SSH 密钥，粘贴保存
# 谷歌就会把上面这段 public key 写入到 ~/.ssh/authorized_keys

```

## 本地通过私钥登录

```
$ ssh -i myKey user@35.189.175.199
Enter passphrase for key 'myKey': (输入 private key 密码)
Welcome to Ubuntu 16.04.2 LTS (GNU/Linux 4.11.2-041102-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

83 packages can be updated.
0 updates are security updates.

Last login: Sun Oct  8 06:40:43 2017 from 115.200.175.117

```

## 通过 SSH 密码验证登录

```
$ ssh user@35.189.175.199
Permission denied (publickey).

# 之所以会出现这种情况，因为谷歌默认把密码验证登录关了，需要自行打开
$ sudo vi /etc/ssh/sshd_config
PasswordAuthentication yes 
:wq!

# 改完要重启 ssh 服务
$ sudo service sshd restart

# 再次连接
$ ssh user@35.189.175.199
user@35.189.175.199's password: (输入实例用户的密码)
Welcome to Ubuntu 16.04.2 LTS (GNU/Linux 4.11.2-041102-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

83 packages can be updated.
0 updates are security updates.

Last login: Sun Oct  8 06:59:24 2017 from 115.200.175.117

# 至此大功告成

```

</div>

</div>
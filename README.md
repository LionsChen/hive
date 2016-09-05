# hive
安装hive
在安装hive之前需要保证hadoop能够正常的运行
首先需要做的事情是下载hive，在官网下载之后传到想要的位置解压，然后在conf文件下，创建hive-site.xml
配置如下：
```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
        <property>
                <name>javax.jdo.option.ConnectionURL</name>
                <value>jdbc:mysql://114.215.133.63:3306/hive</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionDriverName</name>
                <value>com.mysql.jdbc.Driver</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionUserName</name>
                <value>root</value>
        </property>
        <property>
                <name>javax.jdo.option.ConnectionPassword</name>
                <value>yourpasswd</value>
        </property>
        <property>
                 <name>hive.insert.into.multilevel.dirs</name>
                <value>true</value>
        </property>
</configuration>
```

这个配置文件是参照官网上配置数据源的文档，这里是用的MySQL，配置了MySQL的地址和jdbc驱动，登录的用户名和密码等信息

## 安装MySQL
由于hive远程模式，是使用MySQL作为远程数据库存储数据，所以需要用到MySQL数据库，我们可以在一台用来安装数据库的服务器上，
安装上MySQL
在Ubuntu命令行输入命令
```
apt-get mysql.server,mysql.client
```
就会自动安装好MySQL,还会设置好root用户的账户和密码
为了让hive的远程模式可以正常运行，我们需要将MySQL的远程登录权限开放
在安装MySQL的服务器上用root登录
```
root@lionschen:~# mysql -uroot -p
Enter password:

```
这样进入MySQL命令行之后，授权远程登录
为了安全，一般我们就只给自己的服务器的ip授权，并且不使用root用户，而且只赋予hive使用的数据库
我们需要先创建一个数据库用于作为hive的元数据，这里推荐使用Navicat ，使用Navicat来登录可以方便的进行管理。
尽量可以使用命令行创建一个hive数据库，这里不赘述
之后创建用户并将hive数据库授权
```
grant all privileges on hive.* to username@ipaddress identified by 'passwd';
```
这里的表示将hive所有的表的所有权限都赋值给用指定ip登录的用户名为username，并且设置了密码
之后flush
```
flush privileges;
```
这样就可以远程登录hive数据库了
要使用MySQL还需要将jdbc的驱动的jar包，拷贝在hive的lib目录下

导入完jar包之后就可以在hive的bin目录下，先初始化，当然只有第一次需要
```
./schematool -dbType mysql -initSchema
```
完成初始化之后，再执行hive命令，就可以进入hive提示符了。

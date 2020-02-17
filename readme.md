# Openstack搭建配置

1.安装ubuntu16.04以上版本server系统，并在系统中安装git必要工具；

2.命令行输入 `git clone https://github.com/openstack-dev/devstack.git --branch /stable/pike` 拉取openstack自动化配置库devstack；

3.进入devstack文件夹，运行命令`devstack/tools/create-stack-user.sh；`

​                                                         `chown -R stack:stack /opt/stack/devstack.`

​                                          （新建stack用户，并赋予root权限）。

4.在devstack文件夹下创建***local.conf***配置文件，samples文件夹下有相应的示例，也可根据所需要的openstack模块来自定义配置文件内容；

5.执行./stack.sh命令来自动搭建openstack，大约需要20-30分钟安装；

可能遇到的问题:

（1）注意安装第三方库时（例如pbr）要安装在dist-packages下面，防止无法找到库；

（2）openstack安装依赖mysql，如果没有需要手动安装，过程中遇到问题无法解决可以先完全卸载mysql后再重新安装，卸载方法见https://askubuntu.com/questions/640899/how-do-i-uninstall-mysql-completely；

 ①Mysql安装时需要配置root用户密码为123456，对应sql语句：

`set password forroot@localhost=password('666');`

​    ②如无法响应可重新连接：sudo service mysql start
​    3 登陆mysql：

         mysql -uroot -p或mysql -uroot -h 127.0.0.1 -p 
         查看详情：systemctl status mysql.service
​    ④如果报错Can 't connect to local MySQL server through socket '/tmp/mysql.sock '(2) ：建立一个软连接连上socket就可以了 ln -s /var/run/mysqld/mysqld.sock /tmp/mysql.sock

（3）devstack搭建过程如果报错`stack.sh failing giving error "g-api did not start"：`
解决方法是编辑functions-common文件:
注释 `$SYSTEMCTL $ start systemd_service`，并添加 `reload_service $systemd_service`
上一句是启动服务，但是安装过程中服务是已经启动的，所以改为重启；

（4）Linux IPV6相关报错：`RTNETLINK answers: Permission denied`
操作如下，给服务器设置IPV6操作的时候报错。
输入`sysctl -a |grep disable_ipv6`  看到全部都是1 ，大概意思就是"不允许ipv6"的意思
解决方案是把全部变成0  
`vi /etc/sysctl.conf` 
修改`disable_ipv6`的所有变成0
然后`/sbin/sysctl -p`【立即生效】

（5）遇到问题实在无法解决就`./unstack.sh`卸载安装，`./clean.sh`清空，再重新搭建。
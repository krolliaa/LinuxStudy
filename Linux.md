# `Linux`

1. 官网下载`iso`然后新建直接选择最小安装（可以加一个系统基本工具），配置好网络

2. 下载网络工具`yum install net-tools -y`

3. 打开`sshd`直接使用`XSHELL`连接：`systemctl start sshd`

4. 安装`docker`：`yum install docker -y`

5. 为了方便直接把防火墙关了：`systemctl stop firewalld + systemctl disable firewalld`

6. 打开`docker`：`systemctl start docker`

7. 配置`docker`镜像：

   - 到网址：https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

   - ```shell
     sudo mkdir -p /etc/docker
     sudo tee /etc/docker/daemon.json <<-'EOF'
     {
       "registry-mirrors": ["https://kygtc6pu.mirror.aliyuncs.com"]
     }
     EOF
     sudo systemctl daemon-reload
     sudo systemctl restart docker
     ```

8. 安装`MySQL`：

   ```shell
   docker pull mysql:8.0.28
   cd /var/lib/docker/volumes
   mkdir mysql
   cd /var/lib/docker/volumes/mysql
   mkdir conf
   mkdir data
   mkdir logs
   docker run -p 9527:3306 --name mysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password --privileged -d mysql:8.0.28
   cd conf
   touch my.cnf
   yum install vim -y
   vim my.cnf
   --->
   [mysqld]
   skip-name-resolve
   character_set_server=utf8
   datadir=/var/lib/mysql
   server-id=1000
   --->
   docker restart mysql
   设置下，此时本地 Navicat 才能访问：
   docker exec -it mysql bash
   mysql -u root -p
   use mysql
   delete from user where host="%" and user="root";
   # 设置允许使用root用户访问数据库的主机名称，192.168.0.109表示能使用所有的主机使用root用户访问[上次数据库被入侵的事情历历在目还是做好安全吧~]
   update user set host = '192.168.0.109' where user = 'root';
   # 设置root用户密码，mysql_native_password表示密码认证的插件
   alter user 'root'@'192.168.0.109' identified with mysql_native_password by 'password';(设置强密码，别大意)
   # 立即生效
   FLUSH PRIVILEGES;
   Navict 连接即可 ---> 192.168.0.105:9527
   ```
   
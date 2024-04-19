### linux常用

- 端口查询: netstat -nultp
- 当前目录大小: du -sh
- 文件大小: du -h --threshold=1G --max-depth=0 ./*| sort -nr
- tcp连接统计: netstat -antp|grep 192|awk '{print $5}'|cut -d ':' -f1|sort|uniq -c
- root同步时间: ntpdate ntp1.aliyun.com
- 过滤注释和空白: grep -v '#'| grep -v '^$'
- 批量删除日志: find /home/admin/wildfly-14.0.1.Final8080/standalone/log -mtime +2  -name "server.log.*" -exec rm -rf {} \;
- LLG: ll --block-size=M
- llM: ll --block-size=G|grep G
- ls: ls -lht

### linux系统

- CPU型号: cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
- cpu个数: cat /proc/cpuinfo| grep "processor"| wc -l
- 释放内存: echo 3 >/proc/sys/vm/drop_caches
- 内核版本: cat /etc/redhat-release
- 关闭系统启动防火墙: systemctl disable firewalld
- 关闭防火墙: systemctl disable firewalld
- 防火墙状态: systemctl: command not found
- 添加用户并到用户组: useradd -g admin admin
- 添加用户组: groupadd admin

### 其他命令

- 改变用户: sudo chown -R user:userGroup file
- 解压gz: tar -zxvf
- lsof: lsof -i:10002
- 卸载rpm: rpm -qa yum
- localinstall: yum -y localinstall
- tar压缩: tar -czf file.tar.gz file
- rpm: rpm -ivh
- 解压xz: tar xvJf

### java

- 查看最高线程10进制号 :ps aux|sort -k3nr|head -1|cut -c 9-15|xargs ps -o tid -mp|sort -rn|head -1|cut -c 1-6|xargs echo
- 实时最高线程cpu: `ps aux|sort -k3nr|head -1|cut -c 9-15|xargs echo top -Hp`
- cpu最高进程jstack: `ps aux|sort -k3nr|head -1|cut -c 9-15|xargs echo jstack`
- cpu最高线程信息: `ps aux|sort -k3nr|head -1|cut -c 9-15|xargs echo jstack`|`ps aux|sort -k3nr|head -1|cut -c 9-15|xargs ps -o tid -mp|sort -rn|head -1|cut -c 1-6|xargs printf "grep -A20 0x%x\n"`
- 查看所有tid: `ps aux|sort -k3nr|head -1|cut -c 9-15|xargs printf "ps -mp %s -o THREAD,tid,time"`| sort -rn | head -10
- 16进制: printf "%x\n"
- top Hp: top -Hp
- dump: jmap -dump:format=b,file=1.dump pid
- 堆栈: jstack pid

### docker-image

- 批量删除none标签: docker rmi -f `docker images -a | awk '/^<none>/ { print $3 }'`
- 批量删除镜像: docker rmi -f `docker images -a | awk '/liwench*/ { print $3 }'`
- dockfile构建镜像: docker build -t name:tag . -f dockerfile
- 容器生成镜像: docker commit  container_id newName
- 加载镜像: docker load --input
- inspect: docker image inspect
- 导出镜像: docker save -o ubuntu.tar id ubuntu:saucy
- 删除10天未使用镜像: docker image prune -a --force --filter "until=240h"
- 镜像删除: docker image rmi -f

### docker-container

- 导出容器: docker export id > name.tar
- 进入容器: docker exec -it id /bin/bash
- 导入容器: docker import *.tar test:v1
- 查看容器无限制内存占用: docker stats --no-stream|grep -v k8s_POD|grep -v filebeat|grep k8s_|grep '/ 31\.'
- 查看容器内存占用: docker stats --no-stream|grep -v k8s_POD|grep -v filebeat|grep k8s_
- 容器复制文件: docker cp cid:/filepath ./
- 容器日志: docker logs --tail=1000 -f
- 创建容器: docker run --name myname -d name:tag
- 查看已有容器: docker container ls -a

### k8s

- 所有terminating: rancher kubectl get pods --all-namespaces=true |grep Terminating
- 失败的pod: rancher kubectl get pods --field-selector=status.phase=Failed --all-namespaces=true
- describe: kubectl describe pod
- kube-system: kubectl get pod -n kube-system
- deployment: kubectl get deployment x
- deletePod: kubectl delete pod x
- getPod: kubectl get pod x

### redis

- startRedis: redis-server 7001/redis.conf
- 查看所有节点信息: redis-cli -h 127.0.0.1 -p 7001 cluster nodes

### arthas

- dockerArthas: docker exec -it  containerId /bin/bash -c "wget https://arthas.aliyun.com/arthas-boot.jar && java -jar arthas-boot.jar"
- 调用路径: stack demo.MathGame primeFactors 'params[0]<0' -n 2
- 调用堆栈: trace -j  demo.MathGame run
- 时空隧道: tt -t demo.MathGame primeFactors -n 3
- 查看阻塞线程: thread -b
- 查看控制台: dashboard
- 退出连接: exit
- 完全退出: shutdown
- 启动: java -jar arthas-boot.jar

### mysql

- shutdown: mysqladmin  -uroot -proot@xxxx -S /export/mysqldata1/sock/mysql.sock shutdown
- stop: systemctl stop mysqld.service&&mysqld_safe --defaults-file=/etc/my_3306.cnf &

### oracle

- 表分析: analyze table T_XX compute statistics;
- 启动db: startup
- 修改密码不过期: ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
- 查询session: show parameter sessions
- 查询process: show parameter processes
- 修改session: alter system set sessions=3350 scope=spfile;
- dba: sqlplus / as sysdba
- 修改process: alter system set processes=3000 scope=spfile;
- 关闭数据库: shutdown immediate
- 监听状态: lsnrctl status
- 启动监听: lsnrctl start

### jboss

- boot: nohup java -Xmx2048m -Xms1024m -XX:MaxMetaspaceSize=512M -XX:MetaspaceSize=256M -Dfile.encoding=utf-8 -jar ecs-boot.jar > ecs-boot.log 2>&1 &
- 8080: nohup sh /home/admin/wildfly-14.0.1.Final8080/bin/standalone.sh >/home/admin/jbossConsoleLog/consoleLog8080.out 2>&1 &

### atlas

- 查询PID: ps aux|grep  org.apache.atlas.Atlas|grep -v grep

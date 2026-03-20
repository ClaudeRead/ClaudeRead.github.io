### 查看用户
```Plain Text
1. 查看用户信息文件
cat /etc/passwd
2. 查看影子文件
cat /etc/shadow
3. 查看系统是否还存在其他的特权账户，uid为0，默认系统只存在root一个特权账户
awk -F: '$3==0{print $1}' /etc/passwd
cat /etc/passwd | grep x:0
```
### 查看当前登录用户，以及其登录ip。pts代表远程登录，tty代表本地登陆
```Plain Text
who
```

### 查看目前登入系统的用户，以及他们正在执行的程序。
```Plain Text
w
```

### 查看现在的时间、系统开机时长、目前多少用户登录，系统在过去的1分钟、5分钟和15分钟内的平均负载。
```Plain Text
uptime
```

### 查看密码文件上一次修改的时间，如果最近被修改过，那就可能存在问题
```Plain Text
stat /etc/passwd
```

### 查看除了不可登录以外的用户都有哪些，有没有新增的
```Plain Text
cat /etc/passwd | grep -v nologin
```

### 查看能用bash shell登录的用户
```Plain Text
cat /etc/passwd | grep /bin/bash
```

### 查看历史命令
```Plain Text
history
```

### 保存历史命令
```Plain Text
cat .bash_history >>history.txt
```

### 查看端口开放和连接情况
```Plain Text
netstat -pantu
```

### 如果发现可疑外联IP，即可根据对应PID查找其文件路径
```Plain Text
ls -l /proc/pid/exe
```

### 查看进程和关联进程
```Plain Text
ps -aux
ps -aux | grep pid
```

### 查看开机启动项
```Plain Text
systemctl list-unit-files | grep enabled
```

### 定时任务
```Plain Text
crontab  -l
```

### 查看指定用户定时任务
```Plain Text
crontab -u root -l
# 查看root用户任务计划
```

### 进程动态监控，默认根据cpu的占用情况进行排序的，按b可根据内存使用情况排序。
```Plain Text
top
```

### 查看host文件是否被篡改
```Plain Text
cat /etc/hosts
```

### 统计爆破主机root账号的失败次数及ip
```Plain Text
grep "Failed password for root" /var/log/secure | awk '{print $11}' | sort | uniq -c | sort -nr | more
```

### 查看成功登录的日期、用户名、IP
```Plain Text
grep "Accepted " /var/log/secure | awk '{print $1,$2,$3,$9,$11}'
```

### 查看命名修改时间，防止被替换
```Plain Text
stat /bin/netstat
```
<!-- ##{"timestamp":1755314509}## -->
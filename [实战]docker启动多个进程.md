
# 脚本文件
  start_whitecoind
```
#!/bin/bash
#1. firstboot create configure file for whitecoind
#2. Download blockchain data with wget  for saving much times
#2. then  run whitecoind as deamon
#3. Container must mapper port 15815:15815 for whitecoind client connetion
#4.
# usage:
# docker run \
#  -itd \
#  --restart=always \
#  --privileged \
#  --name 0.4 \
#  -p 127.0.0.1:15815:15815
#  -P  \
#  whitecoind:v0.4 \
#  /root/scritp/start_whitcoind
#

/root/script/firstboot
whitecoind

# just keep this script running
while [[ true ]]; do
    sleep 1
done
```

**test 1:**  
   #docker ps   

### test 2:  
docker exec -it 0.4 /bin/bash
//login container (name is 0.4), run /bin/bash
// Ok!

### in container 0.4
```
root@89f09a1b67a1:~# top
  15 root      20   0  657324  58476   9468 S 35.7  7.0   5:16.17 whitecoind                                                                                                                  
    1 root      20   0   18056   2820   2688 S  0.0  0.3   0:00.46 start_whitecoin                                                                                                             
   81 root      20   0   18240   3068   2852 S  0.0  0.4   0:00.23 bash                                                                                                                        
  977 root      20   0   38772   3156   2684 R  0.0  0.4   0:00.00 top                                                                                                                         
  986 root      20   0    4376    812    744 S  0.0  0.1   0:00.00 sleep
  
  root@89f09a1b67a1:~# ps -e
  PID TTY          TIME CMD
    1 pts/0    00:00:00 start_whitecoin
   15 ?        00:07:14 whitecoind
   81 pts/1    00:00:00 bash
 1269 pts/0    00:00:00 sleep
 1270 pts/1    00:00:00 ps

  
  ```
  
  # 结论
    1. 通过脚本可以运行多个任务, 并且脚本带死循环,不让 container 退出
    2. 可以通过 docker exec -it container /bin/bash 再次登录容器,进行管理,调试
    3. 可以看出 两次登录 TTY不同,第一次是 pts/0, 第二次是 pts/1, 后台进程没有TTY, 显示为"?"
    




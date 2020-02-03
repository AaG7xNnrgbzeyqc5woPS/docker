
[如何在一个Docker中运行多个程序进程](https://www.iamle.com/archives/2241.html)  
[构建容器的最佳做法](https://cloud.google.com/solutions/best-practices-for-building-containers?hl=zh-cn)  
[操作容器的最佳做法](https://cloud.google.com/solutions/best-practices-for-operating-containers?hl=zh-cn)  
[twelve-factor app](https://12factor.net/)  

-----------------------------
# Bash Shell脚本

入口文件运行一个Bash Shell 脚本, 然后在这个脚本内去拉起多个进程  
注意最后要增加一个死循环不要让这个脚本退出,否则拉起的进程也退出了  
```
run.sh	
#!/bin/bash
 
# start 1
start1  > /var/log/start1.log 2>&1 &
# start 2
start2 > /var/log/start2.log 2>&1 &
 
# just keep this script running
while [[ true ]]; do
    sleep 1
done
``` 

在Dockerfile的入口中运行run.sh  
ENTRYPOINT  ["run.sh"]  

用Bash Shell 的方式,任意发行版的linux都支持,缺点是不能拉起crash的进程,也就是只能拉起运行不能”守护”  
如果不关心进程crash问题那么可以用这种方式  


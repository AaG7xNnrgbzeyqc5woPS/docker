
任务:
  需要在服务器上编译运行 whitecoind
  
  ```
  //download ubuntu 16.04 images
  docker pull ubuntu:16.04
  docker images  //list docker images
  docker ps -a
  docker ps
  docker info
  docker version
  docker --version
  
  // run unbuntu 16.04 for compliling and running whitecoind
  docker run \
  -itd \
  --privileged=true \
   --name whitecoind \
  -p 15814:15814 \
  -v /root/.whitecoin-xwc:/root/.whitecoin-xwc \
  ubuntu:16.04 /bin/bash
  
  
 //login docker container whitecoind
 docker exec -it whitecoind /bin/bash

//opertion in whitecoind container
cat /etc/issue
uname -a
uname -r
whoami
pwd
exit

docker port whitecoind
docker rm  whitecoind

```


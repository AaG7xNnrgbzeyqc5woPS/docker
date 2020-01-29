
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
  --restart=unless-stopped
  --privileged=true \
  --workdir ~ \
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

------
DOCKFILE
FROM ubuntu16.04
RUN apt-get update && \
        apt-get upgrade -y && \
        apt-get install build-essential -y  && \
        apt-get install libssl-dev -y && \
        apt-get install libdb++-dev -y && \
        apt-get install libboost-all-dev -y && \
        apt-get install libqrencode-dev -y && \
        apt-get install git tmux tree zip nano -y \

RUN  git clone https://github.com/peterli360/whitecoin-1.git  whitecoin && \
        cd whitecoin/src && \
        make -f makefile.unix USE_UPNP=-  && \
        strip whitecoind \

```


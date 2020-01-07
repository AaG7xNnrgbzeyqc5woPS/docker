# install docker at manjaro
    sudo pacman -S docker
    sudo systemctl enable docker
    sudo systemctl start docker
    sudo systemctl status docker

    sudo usermod -aG  docker john
    sudo reboot   //logout
    
    docker info
    docker version
    docker --version
    
    docker run hello-world
    
    
 # 使用脚本安装边锋和测试版
    本示例使用 get.docker.com 上的脚本在 Linux 上安装最新版本的Docker Engine-Community。
    要安装最新的测试版本，请改用 test.docker.com。在下面的每个命令，取代每次出现 get 用 test。
    $ curl -fsSL https://get.docker.com -o get-docker.sh
    $ sudo sh get-docker.sh
    
    
    
    
    
    

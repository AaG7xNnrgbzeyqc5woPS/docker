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
    
    
    
    
    
    

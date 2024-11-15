# OpenVAS/Greenbone Docker Install
Anastasia Reed

CYB 3343 - System Administration

University of Tulsa

Professor Justin Miller

## Install Docker
https://docs.docker.com/engine/install

Select appropriate version, I selected Ubuntu. I installed from the apt repository.

https://docs.docker.com/engine/install/ubuntu/

Set up Docker’s apt repository:
  
      # Add Docker's official GPG key:
      sudo apt-get update
      sudo apt-get install ca-certificates curl
      sudo install -m 0755 -d /etc/apt/keyrings
      sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
      sudo chmod a+r /etc/apt/keyrings/docker.asc
      
     # Add the repository to Apt sources:
      echo \
        "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
        $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
        sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      sudo apt-get update

Install Docker packages (I installed the latest version):

      sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

Verify:

Verify Docker Compose is installed:

      docker compose version

## Get/start container:

      sudo docker run -d -p 443:443 --name openvas mikesplain/openvas

Login via browser to OpenVAS/Greenbone:
`https://localhost/`
  
  - Username: admin
  - Password: admin

Go to Scans, select wizard icon in the top left corner when prompted, confirm that that is the target you wish to scan.
    
  **NOTE:** You can also go to Targets and set it up yourself.

Run scan and wait. For a *while*.

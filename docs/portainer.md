# Portainer

## Introduction
When I first started with my homelab I was searching for a way to easily manage my Docker containers.
On Youtube I quickly found videos about Portainer and how to use Portainer to manage your stacks (basicly your Docker Compose yml files), containers and so on.
As a Docker newbie it was pretty exciting to make my very first Docker Compose file and install Portainer.

## Installating & upadting Portainer
I am running an Ubuntu 22.04.5 LTS VM to run my Portainer installation.
In hindsight this could also be installed in LXC container (I am sure about that).
Since it was a new VM I needed to get Docker to install Portainer and get it running.

### Installing Docker and Docker Compose
1. `sudo apt install curl` (if curl is not installed)
2. `curl -fsSL https://get.docker.com -o get-docker.sh`  
    [Docker Github](https://github.com/docker/docker-install)
3. `sh get-docker.sh`
4. Test if Docker  has been installed by typing: `docker run hello-world`
    Whern you see the text **Hello from Docker!** then all is well.

### Installing Portainer on Docker
1. Create a volume for Portainer server to store its database: `docker volume create portainer_data`
2. Download and install the Portainer Server container: `docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:2.21.4` (2.21.4 is the latest versiom at the time of writing.)
3. You can check if the Portainer Server is: `docker ps:`
    There you should see the Portainer Server container.
4. If everything went you can check if you can access Portainer.
    https://IPADDRESS:9443 (If you used the default port when installing)
5. After the page loads you can create an account and start using Portainer!

Source: [Installing Portainer](https://docs.portainer.io/start/install-ce/server/docker/linux)
  
### Installing the Portainer agent on Docker Standalone server
1. Open the desired server Proxmox or SSH.
2. In Portainer disconnect from your current environment
3. Click on **Environment-related -> Environment**
4. Click in the right corner on **+ Add environment**
5. Then select **Docker Standalone** and click on **Start Wizard**
6. Keep the selection on **Agent and** give the new environment a name and insert the IP-address of the desired host.  
*Remember: You can find the IP-address on your host with the ip a command.*  
7. Copy the Docker run command and paste in your desired server.
8. After these steps your new environment should appear in Portainer from there you have limited management capabilities.  
  
Source: [Install Portainer Agent on Docker Standalone](https://docs.portainer.io/admin/environments/add/docker/agent)

### Updating Portainer
After using Portainer for a while you will get notifications about new versions.  
I recommend to stick to the LTS versions.  
If you decide to update Portainer you can follow these steps below to update Portainer.

1. Before you begin make a back-up of your server (you never know what can go wrong)
2. Login to Portainer and stop all the containers
3. Login to your Portainer server via Proxmox or SSH
4. Stop Portainer: `docker stop portainer`
5. Remove the Portainer image: `docker rm portainer`
6. Pull the new image: `docker pull portainer/portainer-ce:2.21.4` (At the moment 2.21.4 is the newest version)
7. Deploy the updated version of Portainer: `docker run -d -p 8000:8000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:2.21.4` (At the moment 2.21.4 is the newest version)
8. When all went well you can access your Portainer installation with the newest Portainer version.

Source: [Updating Portainer](https://docs.portainer.io/start/upgrade)  
  
### Updating the Portainer Agent on Docker Standalone
Before updating the Portainer Agent it is strongly recommended to make a back-up by me and the developer of Portainer.  
Perform these steps on the host where the Portainer Agent is installed.  
  
1. Open the server in ProxMox or SSH to the server.
2. Stop the Docker container: `docker stop portainer_agent`.
3. Remove the Docker container: `docker rm portainer_agent`.
4. Pull the new image for the Portainer Agent: `docker pull portainer/agent:lts`.
5. Run the new Docker container: `docker run -d -p 9001:9001 --name portainer_agent --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/volumes:/var/lib/docker/volumes portainer/agent:lts`.
6. In Portainer the environment should now show the new version and you should to connect to the updated environment.  
  
Source: [Updating on Docker Standalone](https://docs.portainer.io/start/upgrade/docker)

## Resources
- [Portainer website](https://www.portainer.io/)
- [Portainer Github](https://github.com/portainer/portainer)
- [Install Portainer Agent on Docker Standalone](https://docs.portainer.io/admin/environments/add/docker/agent)  
- [Updating on Docker Standalone](https://docs.portainer.io/start/upgrade/docker)
# Uptime Kuma

## Introduction
With Uptime Kuma you can monitor the status of your hosts running on your homelab.  
Or you can monitor other hosts outside your homelab.  
You can also setup status pages to only show hosts on your Portainer installations and/or running on your Proxmox.  
If you think about Cachet or Statuspage, this is a simpler version of these tools.  

## Deploying Uptime Kuma
Deploying is very simple.  
Just head over to the [Uptime Kuma website](https://uptime.kuma.pet/) and copy the Docker run command.  
If 3001 is already being used in your homelab change the port number XXXX:3001 ***never*** change the port number after the :3001 since that the is the internal port of the Docker container.  
Just like that you are up and running by typing the following in the browser ipaddress:3001 (assuming you did not change the port number).  
If you want to know your ip address on your Ubuntu server you can type `ip a` to find your ip address.  

## Resources
- [Uptime Kuma site](https://uptime.kuma.pet/)
- [Uptime Kuma Github](https://github.com/louislam/uptime-kuma)
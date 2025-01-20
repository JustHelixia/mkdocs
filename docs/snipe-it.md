# Snipe-IT

## Introduction
I wanted to experiment with a Snipe-IT installation and keep track which hardware and license(s) I have.  
After that I wanted to see if I could make some suggestions to improve the experience of Snipe-IT isntallation.  
  
## Installation
I struggled a lot to install Snipe-IT via Docker.    
So I ended up using a helper script to install.  
  
## Getting the helper script on your server
I installed a Ubuntu 22.04 server in a LXC container to deploy Snpie-IT.  
After updating, upgradingand rebooting the server I ran the commaond below:  
`bash -c "$(wget -qLO - https://github.com/community-scripts/ProxmoxVE/raw/main/ct/snipeit.sh)"`

If you do not want to use a helper there are also other ways to install Snipe-IT.  
You can check out their [documentation](https://snipe-it.readme.io/docs/installation).  

Have fun getting started with Snipe-IT!  
Check out their [getting started](https://snipe-it.readme.io/docs/getting-started) section.  
I have learned also new things from the documentation after using Snipe-IT.

## Upgrading Snipe-IT
From time to time it is needed to upgrade the application especially when there are security issues which are fixed.
It is really easy if the permissions are properly set up.

### Starting the upgrade
1. Login to the server via SSH or Proxmox
2. Navigate to the root folder of Snipe-IT (in my case /var/www/html/snipeit)
3. Run the following command `php upgrade.php`  
    If you run into permission issues like me refer to the problem section below.
4. After some time when the upgrade.php ends you are able to login to Snipe-IT again.

### Possible problems

#### Permission denied while upgrading

You will see the following errors while trying upgrading:
````
PERMISSION DENIED (the affected folder)
````
First find the www-data group with the command `cat /etc/group`.

Then perform the commands below:
````shell
sudo chown -R YOURUSERNAME:www-data /var/www/html/snipeit
sudo usermod -aG www-data YOURUSERNAME
sudo chmod -R 775 /var/www/html/snipeit/storage
sudo chmod -R 775 /var/www/html/snipeit/public/uploads
sudo chmod -R 775 /var/www/html/snipeit/bootstrap/cache
````
After these actions you should be able to upgrade Snipe-IT.

## Resources 
- [Snipe-it website](https://snipeitapp.com/)
- [Snipe-it Github](https://github.com/snipe/snipe-it)
- [Snipe-it documentation](https://snipe-it.readme.io/docs/introduction)
- [Permission denied while upgrading](https://github.com/snipe/snipe-it/discussions/13287#discussioncomment-6417122)
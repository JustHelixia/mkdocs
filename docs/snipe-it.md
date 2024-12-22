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
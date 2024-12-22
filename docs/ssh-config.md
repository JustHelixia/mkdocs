# Configuring SSH

## Introduction
I was facing the issue I was not able to SSH to every server in my homelab.  
At my workplace they use SSH key pair to access server remotely and I wanted to set up the same.  
In this guide I describe the steps you need to take to set up SSH on the client and server side.  
  
## Configuring SSH

### Server: Create a new user and configure SSH
On the remote server create the desired user and give the user sudo permissions.  
  
`sudo adduser USERNAME`  
Fill in the new password.  
If you want to fill the info asked then do so, but you can leave everything empty.  
  
And add the new user to the sudoer group:  
`sudo usermod -aG sudo USERNAME`  
 
Then add the new user to the SSH config:   
`sudo nano /etc/ssh/sshd_config`  
  
If need add the line AllowUser or edit the line if it already exists.  
It should look some llike this:  
> AllowUsers USERNAME  
  
Afterwards reload and restart the ssh daemon:  
`sudo systemctl restart sshd`  
  
### Client: Create and SSH key
On your own computer first create a SSH key to connect to the remote server.  
If you already created SSH key pair for another server you can skip these steps.  
`ssh-keygen -t rsa -b 4096`  
  
You can use the default location to save your newly created SSH key.  
I highly recommend the set a passphrase on your key pair.  
Otherwise you can connect to your servers without any authentication.  
  
#### *Copying your SSH public key to the server*  
Copy the new SSH key to the remote server  
`ssh-copy-id USERNAME@SERVERIP`  
  
### Server: Check SSH public key
  
Login as USERNAME  
Check if your public key has been copied to the server.  
`cat ~/.ssh/authorized_keys`  
  
If you get an output that means your public keys has been copied to the server.  
  
### Client: Connect with the SSH key
Verify if you can connect with SSH to your server.  
If that is successful you can disable PasswordAuthentication on your server.
    
**Remember:** Keep your SSH session alive when changing SSH settings.  
  
### Server: Disable SSH password authentication
Open the SSH config file (**sudo nano /etc/ssh/sshd_config**) on the server again and change the PasswordAuthentication line to  
> PasswordAuthentication no  
 
Afterwards reload and restart the sshd service and start a new SSH session.  
`sudo systemctl restart sshd`  
  
If all goes well you can close all the SSH sessions and leave your SSH configuration as is.  
  
## Client: Simplify connecting to your servers
Instead of typing `ssh USERNAME@SERVERIP` we can simplify the process to set up a SSH configuration for every host.  
  
1. Navigate to your ssh folder: 
`cd .ssh`
2. Create the SSH config file: touch config
3. Edit the config file: `sudo vim config`  
(Or any text editor of your choice.).
4. Add for every host this configuration
```` linenums="1" title="SSH host config"
Host YOUR-PERFERRED-NAME
    Hostname SERVERIP
    Port 22
    User USERNAME
````
There can be more complicated setups like using ProxyJump servers.  
This is out of scope for this article and out of my knowledge range.  
  
After adding your hosts you only need to type ssh HOSTNAME and insert your SSH passphrase.  
  
You might even simply it further by adding aliases to your .bashrc or .zshrc files.
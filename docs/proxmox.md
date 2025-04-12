# Proxmox

## Introduction
I ran the Proxmox setup on my mini-pc more times than I want to admit, but it was the very first thing I installed.
This is pretty much needed to run the whole homelab.

### Life saving Proxmox commands
#### Manualy shutdown LXC containers
Life saving commands if a container does not respond in the Proxmox GUI.

1. Connect to the CLI of your Proxmox server
2. Then list the containers: `pct list`
3. Try to shutdown the container in the CLI first: `sudo pct shutdown <CONTAINER-ID>`
4. If that does not work you can force the container to poweroff: `sudo pct shutdown <CONTAINER-ID> --forceStop 1`
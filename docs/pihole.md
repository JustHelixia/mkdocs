# Pi-Hole

## Introduction
I was tried of all the telemetry and adds and I wanted to set up something than ad-block plus in my extension.  
I choose Pi-hole as my solution, because has set it up and I decided to do it again also.

The reasons I wanted to set up Pi-hole:  

 - Lower my dependence on an extension  
 - Lowering the risks of having extensions in the browser  

I installed Pi-Hole and added the following things:  

 - Unbound  
 - Direct my domain look ups locally  
 - Set my local domain by adding my top domain and CNAME records  

### Prerequisites:

- Recommended system requirements:
	- 2 core CPU
	- 8 GB Storage
	- 512 GB RAM

### Preparations:

Make sure your system is up to date and the needed updates are installed:  
 - `sudo apt update && sudo apt upgrade -y` 
 - `sudo apt install curl`  
 - static IP-address in Proxmox or CLI  

#### Changing the DNS-server

##### In the terminal:

 1. `sudo` `nano` `/etc/systemd/resolved.conf`
 2. Pi-Hole: Change the values DNS to the router and FallbackDNS to an external DNS server or a secondary DNS server.
 3. Host: Change the values DNS to Pi-hole and FallbackDNS to an external DNS server or a secondary DNS server.
 4. Reboot the machine
 5. Use the `dig` command to check the DNS settings
##### On a Proxmox container client

1. Navigate to **container -> DNS**
2. Change the DNS server to the Pi-Hole
3. For good measure reboot the container
##### On Proxmox VM client with Cloud-Init

1. Navigate to **VM -> Cloud-Init**
2. Change the DNS servers value to the Pi-Hole IP-address

## Installation:

1. First download and run the installation script:  
   `curl -sSL https://install.pi-hole.net | bash`
2. Follow the prompts
3. Log in with the provided URLs and password
4. Explore Pi-Hole
5. (Optional) Add your local user to the pihole group:  
   `sudo usermod -aG pihole $USER`

## Configuring Pi-Hole
### Adding clients

1. Navigate to **GROUP MANAGEMENT  -> Clients**
2. If the client is known you can click the client to add it
3. When the client is unknown you can  add it by manually inserting its IP-address
4. Optionally you can create groups by navigating to **GROUP MANAGEMENT -> Groups**  
   You can use then later by including or excluding each group from a list. (later more about lists)

### Lists
These are essentially your allow and block lists.
You can use these to block ads, telemetry, suspicious and other stuff.
You can find great lists at [The Firebog](https://firebog.net/).
My advice is to use 1 or 2 of the green links in the category.

Furthermore you can do the same with domains in the Domains tab.
In my case I added my own domain (*helixeus-it.nl*) as a Regex allow.

### Conditional forwarding 
If you like to have your IP-addresses be converted to hostnames then you can set up conditional forwarding.
I have set up conditional forwarding with the following template: 
*true,(ip-address)/(prefix-len),(,ip-router),(domain)*

### Local DNS Records
If you own a domain you can your domain name with CNAME records.
This will handle your domain requests locally.

1. Navigate to **SYSTEM -> Settings -> Local DNS Records**
2. Under List of local DNS records you can add your domain(s)
3. Insert your domain name under Domain.  
   Point to your reverse proxy by inserting the IP-address in the IP-column.  
   Example domain: helixeus-it.nl  
4. Under List of local CNAME records you can add your subdomains unfortunately wildcards do not work when you add a subdomain.  
   Example: docs.helixeus-it.nl
5. Then point the subdomain to your desired domain by inserting it in the target  

## Unbound configuration
To find what Unbound exactly is you can check out [the Pi-Hole documentation about Unbound](https://docs.pi-hole.net/guides/dns/unbound/)  
Perform the installation in the terminal of your PiHole instance.  
1. `sudo apt install unbound`  
2. `wget https://www.internic.net/domain/named.root -qO- | sudo tee /var/lib/unbound/root.hints`  
3. Create the following file - `sudo /etc/unbound/unbound.conf.d/pi-hole.conf`  
4. Copy text below to add the basis configuration:  
  
```conf
server:
    # If no logfile is specified, syslog is used
    # logfile: "/var/log/unbound/unbound.log"
    verbosity: 0

    interface: 127.0.0.1
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes

    # May be set to no if you don't have IPv6 connectivity
    do-ip6: yes

    # You want to leave this to no unless you have *native* IPv6. With 6to4 and
    # Terredo tunnels your web browser should favor IPv4 for the same reasons
    prefer-ip6: no

    # Use this only when you downloaded the list of primary root servers!
    # If you use the default dns-root-data package, unbound will find it automatically
    #root-hints: "/var/lib/unbound/root.hints"

    # Trust glue only if it is within the server's authority
    harden-glue: yes

    # Require DNSSEC data for trust-anchored zones, if such data is absent, the zone becomes BOGUS
    harden-dnssec-stripped: yes

    # Don't use Capitalization randomization as it known to cause DNSSEC issues sometimes
    # see https://discourse.pi-hole.net/t/unbound-stubby-or-dnscrypt-proxy/9378 for further details
    use-caps-for-id: no

    # Reduce EDNS reassembly buffer size.
    # IP fragmentation is unreliable on the Internet today, and can cause
    # transmission failures when large DNS messages are sent via UDP. Even
    # when fragmentation does work, it may not be secure; it is theoretically
    # possible to spoof parts of a fragmented DNS message, without easy
    # detection at the receiving end. Recently, there was an excellent study
    # >>> Defragmenting DNS - Determining the optimal maximum UDP response size for DNS <<<
    # by Axel Koolhaas, and Tjeerd Slokker (https://indico.dns-oarc.net/event/36/contributions/776/)
    # in collaboration with NLnet Labs explored DNS using real world data from the
    # the RIPE Atlas probes and the researchers suggested different values for
    # IPv4 and IPv6 and in different scenarios. They advise that servers should
    # be configured to limit DNS messages sent over UDP to a size that will not
    # trigger fragmentation on typical network links. DNS servers can switch
    # from UDP to TCP when a DNS response is too big to fit in this limited
    # buffer size. This value has also been suggested in DNS Flag Day 2020.
    edns-buffer-size: 1232

    # Perform prefetching of close to expired message cache entries
    # This only applies to domains that have been frequently queried
    prefetch: yes

    # One thread should be sufficient, can be increased on beefy machines. In reality for most users running on small networks or on a single machine, it should be unnecessary to seek performance enhancement by increasing num-threads above 1.
    num-threads: 1

    # Ensure kernel buffer is large enough to not lose messages in traffic spikes
    so-rcvbuf: 1m

    # Ensure privacy of local IP ranges
    private-address: 192.168.0.0/16
    private-address: 169.254.0.0/16
    private-address: 172.16.0.0/12
    private-address: 10.0.0.0/8
    private-address: fd00::/8
    private-address: fe80::/10

    # Ensure no reverse queries to non-public IP ranges (RFC6303 4.2)
    private-address: 192.0.2.0/24
    private-address: 198.51.100.0/24
    private-address: 203.0.113.0/24
    private-address: 255.255.255.255/32
    private-address: 2001:db8::/32
```  
  
#### After the initial configuration  
5. Just to be sure reboot the server to apply the configuration  
6. Perform the following command `dig pi-hole.net @127.0.0.1 -p 5335`  
   The first time this command will be slow, but the second time it should go a bit quicker.  
7. Now we will continue to validate the Unbound configuration by performing additional dig commands.  
8. Perform `dig fail01.dnssec.works @127.0.0.1 -p 5335` this should give you timeouts, because no servers can be reached  
9. The following command should give a NOERROR report: `dig +ad dnssec.works @127.0.0.1 -p 5335`  
  
### Let Pi-Hole use Unbound as DNS-server  
Now the configuration of Unbound is done you now use Unbound within Pi-Hole.  
1. Login to Pi-Hole  
2. Navigate to **SYSTEM -> DNS**  
3. Expand the **Custom DNS servers** option.  
4. If you set up Unbound you can add **127.0.0.1#5335** to point to the Unbound DNS server.  
  
## Useful Pi-Hole command
To close up I want to share some Pi-Hole commands which are useful for me.  
  
### Pihole commands:  
  
- pihole -up - Updating Pi-Hole  
- pihole -v - Checking the Pi-Hole versions  
- pihole -d - Run Pi-Hole diagnostics  
- pihole status - Check the status of Pi-Hole  
- pihole disable 5m - Disable the protection of Pi-Hole  
- pihole enable - Enable Pi-Hole protection  
- pihole -g - Update Pi-Hole gravity (block and allow lists)  

## Resources: 
   
- [Basic install](https://docs.pi-hole.net/main/basic-install)  
- [Post install](https://docs.pi-hole.net/main/post-install/)  
- [Groups Management](https://docs.pi-hole.net/group_management/)  
- [Upstream DNS Provider](https://docs.pi-hole.net/guides/dns/upstream-dns-providers/)  
- [Unbound setup](https://docs.pi-hole.net/guides/dns/unbound/)  
- [Pihole commands](https://docs.pi-hole.net/main/pihole-command/)  
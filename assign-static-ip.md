sudo nano /etc.dhcpcd.conf

roll all the way down to the file and add these lines
I use ethernet so here is what needed to be added:

```
interface eth0
static ip_address=192.168.1.x/24
static routers=192.168.1.1
static domain_name_servers=192.168.1.1 8.8.8.8
```

Ctrl + O
Enter
Ctrl + X

sudo reboot

re-ssh in to the pi with new IP address

## Structure
steve   192.168.1.250
nugget1 192.168.1.251
nugget2 192.168.1.252
nugget3 192.168.1.253
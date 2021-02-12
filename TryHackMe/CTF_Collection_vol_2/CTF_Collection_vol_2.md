<h1>Write Up for CTF collection Vol.2 on TryHackMe</h1>

<h3>EASTER 1</h3>

<p>Visit the robots.txt and we can see hex characters on the bottom</p>
<br><img src='./Screenshot%20(34).png'></img><br>
<p>
Extract the hex character ans remove the spaces and join the hex characters.
Now we can decode this hex. For this we can either use <a href='https://gchq.github.io/CyberChef/'>CyberChef</a> or python console (any other language can be used too)
I am going to do with cyberchef since it's a great tool.
<br><img src='./Screenshot (35).png' /><br>
and with python consoole we can get the flag with this 
<b>bytes.fromhex('45617374657220313a2054484d7b347537306230375f72306c6c5f3075377d')</b></p>

<h3>Easter 2</h3>

<p>
In robots.txt we can see that there is a base64 encoded string. If you try to visit it you will get a 404 error.
We need to decode this string first.
You can use cyberchef again
<br><img src='Screenshot (36).png'/><br>
you will need this recipe in Cyberchef <h5>From Base64 > URL decode > From Base64 > Remove Whitespace > From Base64 > Remove Whitespace > From Base64</h5>
  Visit the /DesKel_secret_base, look at the source code of the page and you will get the flag
</p>

<h3>Easter 3</h3>

<p>
  For this we need to enumerate the IP for files and directories.
  I will use the gobuster tool for this task, you can use any other tool that you are comfortable with.
  <h5>gobuster dir -w /usr/share/dirb/wordlists/common.txt -u http://{IP of your mahcine}</h5>
  I am using the common.txt wordlist since it is mentioned in the hint.
  We can see that there is a /login.
  Visit the /login and look at the source code for the flag
</p>


<h3>Easter 4</h3>

<p>
If you look at the hints we can see that it is asking to look for a sqli
  Capture the post request with burp(if you don't know how to do that, <a href='https://extratechtalk.in/intercept-http-requests-using-burp-suite/'>read</a> this article) and save that request to a file. You can use random creds such as admin:admin.
  Run sqlmap with the request file
  <h5>sqlmap -r post.req -dbs</h5>
  options in the command
  <i>d : Connection string for direct database connection</i><br>
  <i>b : Retrieve DBMS banner</i><br>
  <i>s : Load session from a stored (.sqlite) file</i><br>
  
  This will reveal the That it is MySql database and there are 4 batabases
  1.information_schema
  2.THM_f0und_m3
  3.mysql
  4.performance_schema
  
  Now we can use this to dump all the table contents of the database, THM_f0und_m3
  <h5>sqlmap -r post.req --dbms=mysql -D THM_f0und_m3 --dump</h5>
  We need to wait for some time since it is a time-based attack
  After all the data is dumped, we can see the flag
  <br><img src='./Screenshot (39).png'/><br>

</p>

<h3>Easter 5</h3>

<p>
  Look at the contents of the user table we can see that there are creds for user DesKel<br>
  <br><img src='./Screenshot (40).png'/><br>
  We can crack the hash with <a href='https://crackstation.net/'>Crack Station</a>.<br>
  Crack the hash and you will get the password for the User. Use these creds in /login and you can see the flag.
</p>

<h3>Easter 6</h3>
<p>
If we look at the hint it asks us to look in the response header.
  Look at the response header for the main page and we see the flag in the response
  <br><img src='./Screenshot (37).png'/><br>
</p>


<h3>Easter 7</h3>
<p>
  The hint is talking about the cookie here. If we look at it we can see that there is a cookie <b>Invited=0</b>.<br>
  We can send a modified cookie to get the flag.
  I am going to use cURL for this, you can also use the browser to set the cookie value in the localstorage.
  <h5>curl http://{MACHINE IP} -H 'Cookie: Invited=1' | grep THM </h5>
  I am using the grep command to just get the line  which has 'THM' string in it, since the source code of the webpage is very long.
  <br><img src='./Screenshot (38).png'/><br>
</p>

<h3>Easter 8</h3>
<p>
  If you look at the hint, it looks like a User-Agent.If you look at the webpage we can see that there is a message saying<br> 
  'You need Safari 13 on iOS 13.1.2 to view this message. If you are rich enough'<br>
  This indicates that we need to send a request to the webpage with user-agent as suggested by the message. User-Agent is already given in the hint.
  We can use curl and grep to get the flag.
  <h5>curl http://{MACHINE IP}/ -H 'User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 13_1_2 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.1 Mobile/15E148 Safari/604.1' | grep THM</h5>
</p>


<h3>Easter 9</h3>
<p>
 If you click on the button which says 'Click here' then you are redirected to /ready for a short time and then again redirected to another page.
  We need to intercept the response and look at the content of the webpage. We will burpsuite for this.
  Before that we need to intercept the reponses too.
  Go to <b>Options</b> in the Proxy tab and chekc on the Intercept server responses to intercept the responses too.
  <br><img src='./Screenshot (41).png'/><br>
  Now, click on the button again and now we can the response that is coming from /ready. The flag is in the source code.<br>
  <br><img src='./Screenshot (42).png' /><br>
</p>


<h3>Easter 10</h3>
<p>
  If you click on the link which says 'Btw, I got a free gift for you, Perhaps a subscription voucher. Claim now!', you will redirected to /free_sub with a message saying 'only people came from tryhackme are allowed to claim the voucher.'<br>
  This is indicating that we need to use Referer header as tryhackme.com to get the flag.
  We can use curl to the flag
  <h5>curl http://{MACHINE IP}/free_sub/ -H 'Referer: tryhackme.com' | grep THM</h5>
  <b>NOTE: Don't forget the '/' at the end of URL</b>
</p>


<h3>Easter 11</h3>
<p>
  There is a menu in webpage which allows us to select some items and when you click 'Take it!' it sends a POST request to the server with params dinner={your choice}&submit=submit.<br>
  Therse is a small message on the bottom which says that he prefers egg, so we need to change the dinner to egg and send the post request to the server.<br>
  <h5>curl -X POST http://{MACHINE IP} -d 'dinner=egg&submit=submit' | grep THM </h5>
  <br>This will give us the flag
</p>

<h3>Easter 12</h3>

<p>
  The hint says 'Fake js file'. We might need to look for js files. There is only one js file, jquery-9.1.2.js we can see a hex string which is being converted to a string<br>
  we can use <a href='https://gchq.github.io/CyberChef/'>CyberChef</a> or just decode it inside the python console.
  In python console you can get the flag with
  <h5>bytes.fromhex('4561737465722031322069732054484d7b68316464336e5f6a355f66316c337d')</h5>
</p>

<h3>Easter 13</h3>
<p>
  If you click the 'Click Me' button then you are already redirected to the flag.
</p>

<h3>Easter 14</h3>
<p>
  If you look at the source code you can see that there is an image tag which commented out. It has the base64 encoded data of the image.
  We can use <a href='https://gchq.github.io/CyberChef/'>CyberChef</a> for this.
  <br><img src='./Screenshot (52).png'/><br>
  The flag is in the image
</p>

<h3>Easter 15</h3>
<p>
  In this we need to access the game in /game1. We can see that each letter is getting encoded to different number, even if there is a pattern it seems to change after some letters.<br>
  We can enter the alphabets strings,'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'. This will give code for each character,
  now we can match the code to the character that is given in hints. After mapping we will get this 'GameOver'. Enter this and we will get the flag
</p>

<h3>Easter 16</h3>
<p>
  For this we need to goto /game2. There are three buttons and when you click on them we can see that there is a post request with parameters, button1=button1&submit=submit(the numberdepends on which button you press. You can look at the request in the network tab in the Developers tool)<br>
  We need to click all three buttons at the same time which means that we need to send the paramters for all three buttons together.We can use CURL for this
  <h5><curl -X POST http://{MACHINE IP}/game2/ 'button1=button1&submit=submit&button2=button2&submit=submit&button3=button3&submit=submit' | grep THM/h5>
</p>

<h5>Easter 17</h5>
<p>
  There is a binary string  in the source code of the home page inside a script tag which is calling a function named actz. We need to convert this binary to hexadecimal number.<br>
  We can use python for this. We will follow the particular path
  <h5>Binary > hex > ascii</h5>
  <br><img src='./Screenshot (53).png'/><br>
  <br>We need to put the binary in the quotes so that it is a string and can be interpreted by the int function
</p>

<h3>Easter 18</h3>
<p>
  Looking at the hint suggests that we need to send a custom request header, egg: YES. We can send the request with curl and get the flag<br>
  <h5>curl http://{MACHINE IP} -H 'egg:YES' | grep THM</h5>
</p>

<h3>Easter 19</h3>
<p>
Reading hint doesn't give much. Scrolling through the source code we can see img tag which is pointing towards an image, <b>small.png</b> . Opening the image gives the flag
</p>


<h3>Easter 20</h3>
<p>
  At the bottom of the page some creds are given, DesKel: heIsDumb and we are asked to send a post request.
  We get the flag when we send the post request
  <br><h5>curl http://{MACHINE IP} -d 'username=DesKel&password=heIsDumb' | grep THM</h5>
</p>

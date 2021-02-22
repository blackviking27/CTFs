<h1>Easy PHP</h1>
<h4>Description: 
<br></br>Please note....<br></br>
Note: This chall does not require any brute forcing</h4>
<h4>Category: Web</h4>
<h4>Link: http://easy-php.darkarmy.xyz/</h4>

Visting <b>/robots.txt</b> gives us <b>?lmao</b>. If we visit the <b>?lmao</b> we can see the source code
<br></br><img src='./images/Screenshot (100).png' /><br></br>

We can send out payload through <b>bruh</b> and <b>nic3</b> parameters. So we can enter a pattern that will match the given text and replace that with oue code and to make it executable we will use the <b>/e<b/> flag in regex. Now our payload would look like this
  <h4>/?bruh=system('ls');&nic3=/.*/e</h4>
  But this won't work as it will be detected by <b>is_payload_danger</b> function. This can be easily bypassed with using 'S' instead of 's' in system command( you can do this for any character or the whole string as well).
  Now the payload would be.
  <h4>/?bruh=SyStem('ls');&nic3=/.*/e</h4>
No we can see the files in the dir
<br></br><img src='./images/Screenshot (101).png' /><br></br>
We can read the flag now
<br></br><img src='./images/Screenshot (102).png' /><br></br>

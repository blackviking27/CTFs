## Bad Blog

DESCRIPTION: **We just added analytics to our blogging website. Check them out!**

SOLUTION: On visting the website we can see a login page and since we don't have any creds, let's register a new account. Looking around the page doesn't reveal anything interesting. Let's upload
something to the website and look around. When we visit our `Loggeed in as <your username>` we get a list of users who have visited our post and also their User-Agent is stored by
website. This is interesing to us. Use burp and visit the page again, this time capture the request and change the User-Agent
<br><img src='./img/Screenshot (130).png' /><br>

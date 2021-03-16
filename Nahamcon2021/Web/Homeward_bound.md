## Homeward Bound

DESCRIPTION: **I can't get anything out of this website... can you find anything interesting?**

SOLUTION: Visiting the website just shows us this
<br><img src='img/Screenshot (126).png'/><br>
Looking at the meesage carefully we can understand that the content of the page is only accessible to us from the inside of the network. So in the burpsuite I changed the `Host` 
header in the `GET` request to `127.0.0.1` but that gave the same message, looks like we need to override the Host header. So I used the `X-Forwarded-For` header to do this (I tried other headers too but this was the only one that worked). I added the following line to the get request `X-Forwarded-For: 127.0.0.1` and forwarded the requeest to get the flag.
<br><img src='img/Screenshot (127).png'/><br>

FLAG: `flag{26080a2216e95746ec3e932002b9baa4}`

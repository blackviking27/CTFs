# Sessions

### Description
Find the flag.

### Link
http://34.69.61.54:4777

### Solution
Visiting the website gives us an login page, since we don't know the creds we can try to bypass it with sql but it was not successful. I looked at the source code of the page and found the creds for the page
`iroh:iroh`

After logging in, I looked around the page and found a link to flag but it was just a false link which just Rick rolls you(still can't believe they did that).I tried looking around for the flag but did not get anything.
At the end I looked the cookies saved for this website by the browser and found this, `sessiontoken=UlN7MG5seV9PbmVfczNzc2lvbl90b2szbn0=`. I base64 decoded the value and got the flag

### Flag
RS{0nly_One_s3ssion_tok3n}

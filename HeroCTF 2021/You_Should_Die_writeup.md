# You Should Die
### Category : Web
### Description : Could you retrieve the flag from this Marketing company ?
### URL : http://chall1.heroctf.fr:9000
### Solution

Visting the website gives us an marketing website with very less functionality. If we looka the source code we can see this comment, `For developper : Do not forget to remove admin.php.bak ! Regards,Security Team -->`
Navigating to `/admin.php.bak` gives the file. Looking at the file we can see that the `/admin.php` (I guessed this file existed because the admin.php.bak was there) is using the `logged` parameter to check if we are admin or not.
Now if we send the request `http://chall1.heroctf.fr:9000/admin.php?logged=true` we get an alert saying that `You are not the admin` but we send the same reqeust with curl 
`curl "http://chall1.heroctf.fr:9000/admin.php?logged=true"` we get the flag, this happens because the initial reponse to the request is a 302 redirect and thet's why the browser is automatically redirected and we don't see the flag.

### Flag : Hero{r3d1r3c710n_c4n_b3_d4n63r0u5_57395379}

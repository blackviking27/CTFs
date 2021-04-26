# 0xSSRF
### Category : Web
### Description : Get the flag !
### URL : http://chall1.heroctf.fr:3000
### Solution

This gives us an service which gives the ability to make the request to another website only using the http protocol. We can also see the `Get Flag` button on the navbar which redirects
us to the flag but we get a message saying that we are not from the localhost. So we need SSRF to the localhost, but if you try `http://127.0.0.1:3000/flag` we don't get the flag.
You can try various bypass for this, the one I used was `http://0:3000/flag` and this gave me the flag

### Flag : Hero{cl4ssic_SSRF_byP4pass_251094}

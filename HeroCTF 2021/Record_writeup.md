# Record
### Category : Misc
### Description : Can you find anything special about this domain heroctf.fr ?
### Solution

I started with looking  for other subdomains but didn't find anything, so I started looking at the dns record of the domain `heroctf.fr` and found the flag in the TXT record.

You can use this command to get the flag

`dig heroctf.fr TXT`

Or use this website, https://dnsdumpster.com/

###  Flag : Hero{cl34rtXt_15_b4d}

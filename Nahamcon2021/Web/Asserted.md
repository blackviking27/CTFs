## Asserted

DESCRIPTION: **Time to hit the gym! Assert all your energy! Err, wait, is the saying "exert" all your energy? I don't know...**

SOLUTION: Looking at the complete website we can't find anything interesting or something that will help us with the challenge. The only thing we can see is that all the page are
being loaded using the `page` parameter, `http://challenge.nahamcon.com:31309/index.php?page=about`. If we enter `../` we get this `HACKING DETECTED! PLEASE STOP THE HACKING PRETTY PLEASE`.

Looks like we cannot use the .., lets try to get the index.php file with php wrapper,

`http://challenge.nahamcon.com:31309/index.php?page=php://filter/convert.base64-encode/resource=index`, this gives back the base64 encoded content of the index.php page.
Reading the content of the page we realise that assert function is being used, this can be used to get LFI (Read more about it [here](https://hydrasky.com/network-security/php-assert-vulnerable-to-local-file-inclusion/)).

So our payload to get the flag is `test','a') == false && readfile('/flag.txt') && strpos('test`
*Note: URL encode all the character in the payload*

FLAG: **flag{85a25711fa6e111ed54b86468a45b90c} **

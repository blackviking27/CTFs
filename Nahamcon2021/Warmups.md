# Warmups

### Chicken Wings

DESCRIPTION: **I ordered chicken wings at the local restaurant, but uh... this really isn't what I was expecting...**

FILE : **♐●♋♑❀♏📁🖮🖲📂♍♏⌛🖰♐🖮📂🖰📂🖰🖰♍📁🗏🖮🖰♌📂♍📁♋🗏♌♎♍🖲♏❝**

SOLUTION: If you google the exact file contents, you will get to know that these kind of text are known as Wingding. You can use this [tool](https://lingojam.com/WingDing) to decrypt
given text to get the flag

FLAG: **flag{e0791ce68f718188c0378b1c0a3bdc9e}**

### Pollex

DESCRIPTION: **👍**

FILE: **IMAGE FILE**

SOLUTION: If you open the image file which is just a thumbs up. Running exif tool and strings against the image file gives nothing but if you look at the thumbnail we can see the
flag. Extreact the thumbnail picture with **exif** tool in linux, **exif -e pollex**. WE get the flag

FLAG: **flag{65c34a1ec121a286600ddd48fe36bc00}**

### Shoelaces

DESCRIPTION: **Do you double-knot your shoelaces? You gotta keep'em tied!**

FILE **IMAGE FILE**

SOLUTION: With strings command we can get the flag, **strings shoelaces.jpg | grep flag**

FLAG: **flag{137288e960a3ae9b148e8a7db16a69b0}**

### Eigth Circle

DESCRIPTION: **Abandon all hope, ye who enter here...**

FILE: <code>D'`r#LK\[}{{EUUTet,r*qo'nmlk5ihVB0S!>w<<)9xqYonsrqj0hPlkdcb(`Hd]#a`_A@VzZY;Qu8NMRQJn1MLKJCg*)ED=a$:?>7[;:981w/4-,P*p(L,%*)"!~}CB"!~}_uzs9wpotsrqj0Qmfkdcba'H^]\[Z~^W?[TSRWPt7MLKo2NMFj-IHG@dD&<;@?>76Z{9276/.R21q/.-&J*j(!E%$d"y?`_{ts9qpon4lTjohg-eMihg`&^cb[!_X@VzZ<RWVOTSLpP2HMFEDhBAFE>=BA:^8=6;:981Uvu-,10/(Lm%*)(!~D1</code>

(NOTE: All the text is part of the challenge)

SOLUTION: This is peice of code from programming language Malbolge. Use this [tool](https://www.tutorialspoint.com/execute_malbolge_online.php) to run the code and get the flag

FLAG: **flag{bf201f669b8c4adf8b91f09165ec8c5c}**

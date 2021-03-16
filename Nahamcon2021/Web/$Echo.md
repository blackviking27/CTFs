## $ECHO

DESCRIPTION: **So I just made a hardcoded bot that basically tells you what you wanna hear. Now usually it's a $ for each thing you want it to say but I'll waive the fee for you if you beta test it for me.**

SOLUTION: Looking at the website, we can input some text in the input and on pressing the echo button just returns the text we entered. If we input any symbols then it just gives
a this message,`Hey mate, you seem to be using some characters that makes me wanna throw it back in your face >:(` and on en entering a very long text gives us this message, ` Man that's a mouthful to echo, what even?`
But if we enter backtick( \` ) we don't get any error. This  good since backtick can be used to execute command in php. List the content of the directory with **\`ls\`**. The flag is present in the
previous directory. If we try to read the files with **\`cat ../flag.txt\`**, we get the length error message. After looking around the web I found a way to read file file without **cat**
command. We can get the flag **\`< ../flag.txt\`**

FLAG: `flag{1beadaf44586ea4aba2ea9a00c5b6d91}`

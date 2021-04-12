#  DababyWeb

### Description
Dababy wanted to share a message, but he seemed to put it too high up...

### Link
34.72.118.158:6284

### Solution

Visiting the page gives us a website dedicated to dababy and it has 2 links. The first link takes you to a wweb page which takes your input and gives it back in the website and the second link is just a webpage with some text.
In first link, If you try to execute some shell commands with functions such as exec, shell_exec and system we get nothing back but there is another way to execute commands in php and that is `double backtics`.
We can execute shell commands as ***\`ls\`***, this gives only a single file, `dababy.sh`. If you try to read the file through the same place you cannot get anythong.
Now in the second website you can see there is a parameter `/fun1.php?file=suge` in the url. We can try to read the file through the `file` parameter. I entered the file name `dababy.sh`
and got the contents of the file. Now we need to get the flag. Enumerate the previous directory with ***\`ls ..\`*** and we can see the `flag.txt` file. Give the relative path in the `file` parameter and we get the flag

### Flag
RS{J3TS0N_M4D3_4N0TH3R_0N3}

PS : Read this writeup as he goes into more details, https://github.com/Ch3lLIST4/CTF-Writeups-2021/blob/main/RITSEC-CTF-2021/DababyWeb.md

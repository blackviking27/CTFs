h1>Meme_stash</h1>
<h4>Description: <p>White Wolf cloned this website for some memes while browsing some memes he stumbled upon the flag but now he is not able to find it can you help him
Note: This chall does not require any brute forcing</p>
<h4>Category: Web</h4>
<h4>Link: http://meme-stash.darkarmy.xyz</h4>

If you visit the /.git we can see a forbidden message (I found this dir as a hit and trial method to look for default dirs). We can get the files using the <a href='https://github.com/arthaud/git-dumper'>git dumper</a>
This will download the files for you. Now if you run <b>git show</b> in the dir which has the files then we can see that at some point there was a flag.jpg in the index.html.
We can use <a href='https://github.com/internetwache/GitTools'>Git Tools</a> to extract the deleted file.
Run the <b>extractor.sh</b>, which is in the Extractor folder and specify the dir which has the website's file and the dir you want to extract to
<h4>./extractor.sh {Dir which has the website's git files} {Destination dir}</h4>
Now inside the extracted files you can find the flag at <b>2-146831302c94e3dfed16c9a90baed8770a82b544/meme_stash</b>
<h4>FLAG: darkCON{g1t_d4_fl4g}</h4>

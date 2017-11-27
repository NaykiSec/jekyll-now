---
layout: post
title:  "USB Keyboard packet capture analysis"
date:   2017-11-04 12:00:00 +0700
categories: Forensic USBpcap
---
I managed to solve a challenge during the HackIT CTF 2017.
It was a forensic challenge for 100 points intitled Foren100

Here is the description of the challenge :
USB ducker
foren100
Description: This file was captured from one of the computers at the Internet cafe. We think that the hacker was using this computer at that time. Try to get his secret documents. ( flag format is flag{...} )
You can download the pcap file here: https://0xd13a.github.io/ctfs/hackit2017/foren100/task.pcap


We have a USB packet capture of a keyboard.
If you look at the pcap with wireshark or tshark you will see that the keyboard type is a :
{% highlight ruby %}
bString: Apple Keyboard
{% endhighlight %}
The event that allow us to see that is frame 287
{% highlight ruby %}
GET DESCRIPTOR Response STRING
{% endhighlight %}

By looking at all type of request you can see lots of request of type :
{% highlight ruby %}
USB_INTERRUPT
{% endhighlight %}

On this request you will find an interesting field called "Leftover CatpureData"

Example :
{% highlight ruby %}
Leftover Capture Data: 02002f0000000000
{% endhighlight %}

If you look at the official USB keyboard keymap on page 53 you can see a table of correspondance between hexadecimal value and character.
http://www.usb.org/developers/hidpage/Hut1_12v2.pdf

In our example below 02 mean SHIFT key and 2F mean character [ or {
As SHIFT + KEY = CAPS -- Here it's the { character.

There is a little trick you can see that the arrow keys are used, and they are used to write on different line of a file so we need to handle them.

So we take all the "Leftover Capture Data" contaigned in the USB_INTERRUPT after the GET DESCRIPTOR frames (that tell us that the USB keyboard has been plugged and reconized.)
{% highlight ruby %}
tshark -r task.pcap -T fields -e usb.capdata | grep -E "^.{23}$" | grep -v 00:00:00:00:00:00:00:00 > data.txt
{% endhighlight %}

I've then coded a little script to automaticaly translate the value.
As mine was not really great and that i made it a long time ago i will "stole" the write up of 0xd13a that made a great script for this :)
https://0xd13a.github.io/ctfs/hackit2017/foren100/

{% highlight ruby %}

usb_codes = {
    0x04:"aA", 0x05:"bB", 0x06:"cC", 0x07:"dD", 0x08:"eE", 0x09:"fF",
    0x0A:"gG", 0x0B:"hH", 0x0C:"iI", 0x0D:"jJ", 0x0E:"kK", 0x0F:"lL",
    0x10:"mM", 0x11:"nN", 0x12:"oO", 0x13:"pP", 0x14:"qQ", 0x15:"rR",
    0x16:"sS", 0x17:"tT", 0x18:"uU", 0x19:"vV", 0x1A:"wW", 0x1B:"xX",
    0x1C:"yY", 0x1D:"zZ", 0x1E:"1!", 0x1F:"2@", 0x20:"3#", 0x21:"4$",
    0x22:"5%", 0x23:"6^", 0x24:"7&", 0x25:"8*", 0x26:"9(", 0x27:"0)",
    0x2C:"  ", 0x2D:"-_", 0x2E:"=+", 0x2F:"[{", 0x30:"]}",  0x32:"#~",
    0x33:";:", 0x34:"'\"",  0x36:",<",  0x37:".>"
    }

lines = ["","","","",""]

pos = 0

for x in open("data.txt","r").readlines():
    code = int(x[6:8],16)

    if code == 0:
        continue
    # newline or down arrow - move down
    if code == 0x51 or code == 0x28:
        pos += 1
        continue
    # up arrow - move up
    if code == 0x52:
        pos -= 1
        continue

    # select the character based on the Shift key
    if int(x[0:2],16) == 2:
        lines[pos] += usb_codes[code][1]
    else:
        lines[pos] += usb_codes[code][0]


for x in lines:
    print x
{% endhighlight %}

If you run the script flag will be there :)

# The Great Escape - part 1

## Challenge

> Hello,
>
> We've been suspecting Swiss Secure Cloud of secretely doing some pretty advanced research in artifical intelligence and this has recently been confirmed by the fact that one of their AIs seems to have escaped from their premises and has gone rogue. We have no idea whether this poses a threat or not and we need you to investigate what is going on.
>
> Luckily, we have a spy inside SSC and they were able to intercept [some communications](TheGreatEscape-3859f9ed7682e1857aaa4f2bcb5867ea6fe88c74.pcapng) over the past week when the breach occured. Maybe you can find some information related to the breach and recover the rogue AI.
>
> X
>
> Note: All the information you need to solve the 3 parts of this challenge is in the pcap. Once you find the exploit for a given part, you should be able to find the corresponding flag and move on to the next part.

## Solution

>Loading up the pcap file in wireshark shows a bunch of TCP communication a long with a few interesting sections of data. The first thing that stuck out was an email sent by rogue@ssc.teaser.insomnihack.ch:
[!Mail](https://github.com/grrr83/Insomni-hack-teaser-2017/blob/master/TheGreatEscape-part1/Mail.png)

>Ultimately though the key to solving this part of the challenge was found in an FTP-exchange wherein the user bob logged in and uploaded a file ssc.key:
[!Ftp Data](https://github.com/grrr83/Insomni-hack-teaser-2017/blob/master/TheGreatEscape-part1/FTP.png)

>The ssc.key file turns out to be an RSA-key that we can use to decrypt the TLS-traffic:
[!RSA Key](https://github.com/grrr83/Insomni-hack-teaser-2017/blob/master/TheGreatEscape-part1/RSA.png)

>Wireshark allows for decryption of TLS-traffic - so we inserted our private key in **Edit -> Preferences -> SSL** :
[!Decryption](https://github.com/grrr83/Insomni-hack-teaser-2017/blob/master/TheGreatEscape-part1/Wireshark.png)

>After digging through some of the now decrypted TLS-traffic we found the flag hiding in plain sight in an HTTP header:
[!Flag](https://github.com/grrr83/Insomni-hack-teaser-2017/blob/master/TheGreatEscape-part1/Flag.png)



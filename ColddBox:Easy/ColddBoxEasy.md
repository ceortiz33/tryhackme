# ColddBoxEasy TryHackme Walkthrough

![]/images/bakcground.PNG

As usual we start with nmap to know the open ports of the machine

We can use this command to accelerate the results of nmap scan

`nmap  -p- -T5 -n -Pn --open --min rate 5000 -vvv <ip-address>`

![](images/nmap1.png)

Looks like port 80 and port 4512 are opened so with this information now we can proceed to scan the services running in these ports

![](images/nmap2.png)

Port 80 is running Wordpress so we can run wpscan to get more information about the server

This options enumerate all the information available in the box.

`wpscan --url http://<ip/address>`

![](images/wpscan1.png)

No plugins were found, but we got an outdated theme that maybe could be exploitable. Making a search in Google looks like this theme is vulnerable to DOM-based XSS https://blog.sucuri.net/2015/05/jetpack-and-twentyfifteen-vulnerable-to-dom-based-xss.html However requires some social engeneering and could be hard to exploit.

![](images/wpscan2.png)

No backups files were found during the scan.

![](images/wpscan3.png)



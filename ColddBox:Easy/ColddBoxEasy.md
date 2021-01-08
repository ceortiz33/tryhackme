# ColddBoxEasy TryHackme Walkthrough

![]/images/bakcground.PNG

As usual we start with nmap to know the open ports of the machine

We can use this command to accelerate the results of nmap scan

`nmap  -p- -T5 -n -Pn --open --min rate 5000 -vvv <ip-address>`

![](images/nmap1.png)

Looks like port 80 and port 4512 are opened so with this information now we can proceed to scan the services running in these ports

![](images/nmap2.png)



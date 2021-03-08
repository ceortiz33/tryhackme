# Archangel TryHackme Walkthrough

<p align="center">
  <img width="400" height="400" src="https://github.com/ceortiz33/tryhackme/blob/main/Archangel/images/archangel_logo.png">
</p>

We begin with an nmap scan in two phases. First phase identify all ports open and then the second scan is used to identify the versions of those ports.

`nmap -p- -T4 -Pn -n --min-rate=5000 -oG Allports 10.10.179.65`

![](/images/allports.png)

![](/images/targeted.png)

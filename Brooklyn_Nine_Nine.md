**Brooklyn Nine Nine** is a simple CTF on TryHackMe aimed at beginner level hackers. 

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

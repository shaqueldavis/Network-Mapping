

For this portion of the project we will need to change our Virtual Machines network settings from NAT to Bridge. This will move your VM from an isolated network on your host computer and place it on the same network as your home router. In our case there is essentially  no risk in doing this. However, we will take a few precautionary steps. 

## Precisionary Steps & Warnings 

First: <br>
**Once your VM is bridged, you should assume it can directly communicate with—and potentially affect—other devices on your home network.** That makes bridged mode a poor choice for experimenting with live malware. <br>
We will not be experimenting with malware in this lab, but if you were previously using your VM to do so, or intend to do so in the future, it’s important to note that having your VM in bridge mode could put your network at risk. <br>
If you installed this Slingshot VM from SANS.org and have only used it to work on this lab, you're fine. If you for whatever reason used this VM for any other purpose, particularly malware experimentation, you should download the Slingshot VM again and start from a clean slate. <br>
**Don’t be spooked**, this is just a disclaimer to make sure we stay safe. More than likely you don't need to worry about this at all. It's just worth mentioning because some SANS courses do require you to experiment with malware using your VMs.

Second: <br>
We will be using Nmap to scan our home network. The scans we will be using are perfectly fine. However I would not recommend performing aggressive scans such as sudo nmap `--script vuln 192.168.1.50`, or `sudo nmap --script exploit 192.168.1.50`. These commands could interfere with sensitive devices such as IoT appliances. A power cycle or network reconnect would most likely resolve such a problem. <br>
**Again, no need to get spooked**. This is just a warning to be extra safe. 😁

Third: <br>
Simply as a security best practice, we will turn on Ubuntu’s firewall before switching to bridge mode. 


### Turn on Ubuntu's Firewall

from a normal terminal prompt check to see if the fire wall is already active first:
```bash
sudo ufw status
```

<img width="2154" height="126" alt="image" src="https://github.com/user-attachments/assets/0aa6d61d-5916-4813-94d6-10e8b19926ee" />

If you see `Status: inactive` you know it is off.

<br>
<br>


Step 1.2
Discover the local gate way of your VM.
```bash
route -n
```

<img width="2074" height="376" alt="Screenshot 2026-07-24 161017" src="https://github.com/user-attachments/assets/9dcb8942-f16b-4fb1-a88a-f4e914bfcdea" />

<br>
<br>

Check to make sure you have nmap on your system:
```bash
nmap --version
```

<img width="2870" height="428" alt="image" src="https://github.com/user-attachments/assets/fbc91762-5008-41ba-b6dc-85cdc136f1ba" />

<br>
<br>

Scan for live hosts on your network:
```bash
nmap -sn 203.0.113.0/24
```


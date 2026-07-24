


Step 1.2
Discover the local gate way of your VM.
```bash
route -n
```

<img width="2074" height="376" alt="Screenshot 2026-07-24 161017" src="https://github.com/user-attachments/assets/9dcb8942-f16b-4fb1-a88a-f4e914bfcdea" />


Scan for live hosts on your network. Use the nmap tool combined with the host discovery scan parameter <-sn> on your subnet range. Run this command once in the morning. Once in the afternoon and once in the evening everyday for one week. In a home network most devices will only be live at certain times but not always. Seeing which host is up and when will give you a better understanding of what IP address correlates with which device on your base line list. As it is made clear, modify your baseline document and add IP addresses to match each host. COMMAND: nmap -sn 203.0.113.0/24 (replace 203.0.113.0/24 with your subnet range. Prepending sudo to the command, if you have permission. it will give a more thorough scan, e.g. revealing the host computer IP address if you are scanning on a VM.)

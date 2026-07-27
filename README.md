#  Network Managment Home Lab

## $\color{blue}{\text{Objective}}$

This project walks you through the process of building and maintaining a home lab for network assets. This entails creating a database for endpoints with PostgreSQL, establishing a baseline inventory of authorized devices, protecting inventory integrity by generating SHA-256 hashes, storing a signed ASCII-armored encrypted copy with GPG, performing host discovery with nmap through Linux Bash automated scripts and cron, and various other tips and tricks that provide support along the way.

### Requirements

This walkthrough will require you to download the Community Edition **Slingshot Linux Distribution** from the official SANS.org website.

<a href="https://www.sans.org/tools/slingshot"><img src="https://img.shields.io/badge/-Download Link-0072b1?&style=for-the-badge&logo=SANS&logoColor=white" /></a>

This walkthrough also assumes that you have familiarity with downloading and operating virtual machine software such as VMWare and VirtualBox. Here we will be using **VMWare Workstation Pro**. 

Before beginning you will also want to create a list of all of your endpoint devices from your network. Most routers come with an app that allows you manage your network. You can usually find a list of active and sometimes even cached devices on your network through the app. <br>
As an alternative you can also visit a weblink to control your router via a web browser. This information is usually found on the back or bottom of your router labeled as something along the lines of “Router Login” or “Network Settings URL”. Here you will also be able to find a list of devices connected to your network. <br>
If the host names are generic, e.g. iphone, I would recommend making a note off every device and then disconnecting them from the wifi one by one to determine which is which.

### Skills Learned

This Project covers:

- Linux Bash scripting for automation
- Nmap host discovery 
- Crontab scheduling
- Creating and navigating a PostgreSQL database
- Network monitoring
- Endpoint managment and tracking

### Tools Used
```bash
Have Chat GPT break down the entire project once compleated and list all tools used. Then paste them here broken down into sections with emoji icons and breif descriptions of the purpose and benifit of each tool
```

## $\color{blue}{\text{Steps}}$
```bash
list all steps here. you can also have chat GPT break this down for you
```
### Phase 1: <a href="Endpoint Database.md">Create Inventory Database for Network Endpoints</a>

### Phase 2: <a href="Allow List (Network Baseline).md">Create List of Allowed Endpoints on Network</a>

### Phase 3: <a href="Automated Scans.md">Automate Network Scans and Baseline Checks</a>




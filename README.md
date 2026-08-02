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

### Skills Demonstrated

- Designed and maintained a PostgreSQL endpoint-inventory database.
- Selected appropriate PostgreSQL data types, including `INET`, `MACADDR`, timestamps, and primary keys.
- Used SQL to create, query, update, filter, sort, and export endpoint records.
- Created PostgreSQL roles and assigned database, schema, table, and sequence permissions.
- Established an allow-list baseline of approved devices using IP and MAC address data.
- Configured VMware NAT and bridged networking for local network visibility.
- Applied host-based firewall rules with UFW before placing the VM on the LAN.
- Identified the local subnet and performed ARP-based Nmap host discovery.
- Generated and interpreted normal, greppable, and XML Nmap output.
- Developed Bash scripts using variables, environment overrides, conditional logic, pipes, file checks, exit codes, and output redirection.
- Used embedded Python to parse CSV and XML data.
- Normalized and validated MAC addresses with Python and regular expressions.
- Correlated Nmap scan results against an approved endpoint baseline.
- Classified devices as approved, unknown, or unverified based on available evidence.
- Implemented error handling for missing files, malformed data, duplicate MAC addresses, and XML parsing failures.
- Created timestamped reports and maintained a symbolic link to the newest report.
- Scheduled dependent scripts with cron and centralized their output in a log file.
- Applied Linux file permissions to restrict script access and allow execution.
- Documented sensitive network information while redacting identifying endpoint data.

### Tools and Technologies Used

#### Primary Tools

- **Nmap** — Performed ARP-based host discovery and generated normal, greppable, and XML scan results.
- **PostgreSQL and psql** — Created and maintained a structured inventory of authorized network endpoints.
- **Bash** — Automated network scanning, output generation, file validation, report creation, and exit-code handling.
- **Python 3** — Parsed Nmap XML and the approved CSV baseline, validated MAC addresses, and classified devices.
- **Cron/Crontab** — Scheduled recurring scans and baseline-comparison scripts.
- **UFW** — Applied host-based firewall rules before connecting the VM directly to the home network.
- **VMware Workstation Pro** — Hosted the Slingshot Linux VM and configured NAT and bridged networking.
- **SANS Slingshot Linux** — Provided the Linux environment used to conduct the lab.

#### Supporting Technologies and Formats

- **SQL** — Created tables and roles and performed queries, inserts, updates, filtering, sorting, and permission grants.
- **CSV** — Stored the exported allow list of approved endpoints.
- **XML** — Provided structured Nmap results for automated parsing.
- **Regular expressions** — Validated MAC-address formatting in the comparison script.
- **systemd utilities** — Verified PostgreSQL service status and startup configuration.
- **timedatectl** — Configured the VM’s time zone for accurate scheduled execution and timestamps.
- **Nano and Gedit** — Created and edited scripts and cron entries.

## $\color{blue}{\text{Steps}}$

### Phase 1: <a href="Endpoint Database.md">Create Inventory Database for Network Endpoints</a>

### Phase 2: <a href="Allow List (Network Baseline).md">Create List of Allowed Endpoints on Network</a>

### Phase 3: <a href="Automated Scans.md">Automate Network Scans and Baseline Checks</a>




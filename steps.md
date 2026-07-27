# Automate Regular Network Scans


For this portion of the project we will need to change our Virtual Machines network settings from NAT to Bridge. This will move your VM from an isolated network on your host computer and place it on the same network as your home router. In our case there is essentially  no risk in doing this. However, we will take a few precautionary steps. 

### 1. Warnings 

First: <br>
**Once your VM is bridged, you should assume it can directly communicate with—and potentially affect—other devices on your home network.** That makes bridged mode a poor choice for experimenting with live malware. <br>
We will not be experimenting with malware in this lab, but if you were previously using your VM to do so, or intend to do so in the future, it’s important to note that having your VM in bridge mode could put your network at risk. <br>
If you installed this Slingshot VM from SANS.org and have only used it to work on this lab, you're fine. If you for whatever reason used this VM for any other purpose, particularly malware experimentation, you should download the Slingshot VM again and start from a clean slate. <br>
**Don’t be spooked**, this is just a disclaimer to make sure we stay safe. More than likely you don't need to worry about this at all. It's just worth mentioning because some SANS courses do require you to experiment with malware using your VMs.

Second: <br>
We will be using Nmap to scan our home network. The scans we will be using are perfectly fine. However I would not recommend performing aggressive scans such as sudo nmap `--script vuln 192.168.1.50`, or `sudo nmap --script exploit 192.168.1.50`. These commands could interfere with sensitive devices such as IoT appliances. A power cycle or network reconnect would most likely resolve such a problem. <br>
**Again, no need to get spooked**. This is just a warning to be extra safe. 😁

### 2. Precisionary Step

Simply as a security best practice, we will turn on Ubuntu’s firewall before switching to bridge mode. 

From a normal terminal prompt check to see if the firewall is already active first:
```bash
sudo ufw status
```

<img width="2154" height="126" alt="image" src="https://github.com/user-attachments/assets/0aa6d61d-5916-4813-94d6-10e8b19926ee" />

If you see `Status: inactive` you know it is off.

<br>
<br>

Set the baseline rules for the firewall to default deny incoming and default allow incoming
```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

<img width="2622" height="492" alt="image" src="https://github.com/user-attachments/assets/69a38bf3-a138-47cf-8d06-54df4a19ea1e" />

Unless you specifically create a rule allowing a connection, it will be blocked. However, your VM is allowed to initiate connections to other devices. 

<br>
<br>

Now enable the firewall:
```bash
sudo ufw enable
```

<img width="2158" height="216" alt="image" src="https://github.com/user-attachments/assets/e761c462-7786-4c3e-86d8-2c1f9560bdae" />

<br>
<br>

Show the updated status and rules:
```bash
sudo ufw status verbose
```
<img width="2430" height="460" alt="image" src="https://github.com/user-attachments/assets/82e26a73-995c-4db6-ad6d-70fdb24758a0" />

<br>
<br>

### 3. Switch VM to Bridge Mode

I will walk you through the process of performing this action with VMWare Workstation Pro. If you are using VirtualBox you can utilize OSINT to find instructions. There are plenty of reputable sources. You can even try Chat GPT. 

Close out of the terminal and shut down the VM:

<img width="1474" height="520" alt="image" src="https://github.com/user-attachments/assets/03c00529-513a-41dd-ab97-ed725244d18c" />

The VM must be restarted in order for the change from NAT to bridge to take effect. In this case we are simply shutting it down, making the change, and starting it back up after.

<br>
<br>

From the top menu bar, select VM > settings, or hit ctrl + D.

<img width="1448" height="688" alt="Screenshot 2026-07-24 223507" src="https://github.com/user-attachments/assets/c30f85ef-e918-4245-981d-1228cabac498" />

<br>
<br>
<br>
<br>

From the Hardware tab, choose Network Adapter. It should be set to NAT by default.

Switch it to `Bridged`, check the box for `Replicate physical network connection state` and click ok.

When the check box is enabled, VMware tries to make the VM's network connection mirror the state of your physical computer's network adapter. 

Without this option, the VM may think it's still connected even after the host has changed networks, sometimes requiring you to disable/re-enable the virtual adapter or reboot the VM. 

<img width="1494" height="1434" alt="Screenshot 2026-07-24 224212" src="https://github.com/user-attachments/assets/07176e0a-24ea-4118-9203-74c87a41db85" />

<br>
<br>
<br>
<br>

### 4. Perform a Network Scan

Restart your VM and open a terminal window.

confirm that Bridged mode is working correctly:
```bash
route -n
```
Your Gateway should be set to the same IP address as your home router.

<img width="2420" height="530" alt="Screenshot 2026-07-24 232006" src="https://github.com/user-attachments/assets/659fa955-b52b-4840-a001-bc6644705567" />
I have redacted some of my network information simply as a security best practice.

The IP address under the Gateway column is what you're looking for. This gives you your subnet range, which we will need later for our network scans with nmap.

<br>
<br>

Check to make sure you have nmap on your system:
```bash
nmap --version
```

<img width="2870" height="428" alt="image" src="https://github.com/user-attachments/assets/fbc91762-5008-41ba-b6dc-85cdc136f1ba" />

<br>
<br>

Test nmap to make sure it's working by performing a simple host scan for live hosts on your network:
```bash
sudo nmap -sn 192.168.x.0/24
```
replace the IP address with your routers IP address.

You should see a list of all hosts currently active on your network. Most if not all of these IP addresses should look familiar. 

Now it's time to automate this scan with a bash script. Before we do, we want to find out where nmap is located. To do this run:
```bash
Which nmap
```

<img width="2120" height="150" alt="image" src="https://github.com/user-attachments/assets/8a6f1df8-78f9-4a3f-9320-a2b352a39f50" />

This will reveal the path to nmap on your VM. You will need this for the script.

<br>
<br>

Create a file with gedit: 
```bash
gedit host-scan-script-nmap.sh &
```

The gedit text opener will open up. Copy the script below and paste it into the editor.
```bash
#!/usr/bin/env bash
# host-scan-script-nmap.sh
# Runs an Nmap host-discovery scan and saves the results in greppable format.
# The script may be executed manually or scheduled through cron.

#########################################
#  THIS AREA MAY REQUIRE MODIFICATION
#########################################
# Replace this with your LAN/subnet if desired
SUBNET="${SUBNET:-192.168.1.0/24}"

#########################################
#  THIS AREA WILL REQUIRE MODIFICATION
#########################################
# Output directory (will be created if missing)
OUTDIR="${OUTDIR:-$HOME/Desktop/Network-Mapping/network-scan-logs}"
mkdir -p "$OUTDIR"

#########################################
#  THIS AREA MAY REQUIRE MODIFICATION
#########################################
# Absolute path to the Nmap executable.
# Verify with: which nmap
NMAP="/usr/bin/nmap"

# --- PREPARE OUTPUT FILE (single greppable file) ---
TIMESTAMP="$(date +%Y%m%d-%H%M%S)"
OUTBASE="$OUTDIR/nmap-scan-${TIMESTAMP}"

GNMAP_FILE="${OUTBASE}.gnmap"
NORMAL_FILE="${OUTBASE}.txt"
XML_FILE="${OUTBASE}.xml"

# Use sudo if it is available without a password prompt;
# otherwise run Nmap as the current user.
if sudo -n true 2>/dev/null; then
  SUDO="sudo"
else
  SUDO=""
fi

# print a clear timestamped header so cron.log is easy to read
echo "=== [$(date '+%F %T %Z')] Running Nmap scan ==="
echo "Grepable output: $GNMAP_FILE"
echo "Normal output:   $NORMAL_FILE"
echo "XML output:      $XML_FILE"

# Run nmap 
# -sn: host discovery only
# -PR: use ARP discovery on the local Ethernet network
# -oG: grepable output for simple IP extraction
# -oN: human-readable output
# -oX: XML output containing structured IP and MAC information
${SUDO} "$NMAP" \
  -sn \
  -PR \
  "$SUBNET" \
  -oG "$GNMAP_FILE" \
  -oN "$NORMAL_FILE" \
  -oX "$XML_FILE"

RC=$?

# print a footer summarizing result and exit code for easy log parsing
echo "=== [$(date '+%F %T %Z')] Nmap finished (exit code: $RC) ==="
# 

exit $RC
```
In this script you may need to adjust the subnet to make sure it is in alignment with your network. You will need to change the OUTDIR (output) path so that. You may also need to change the path for nmap to the appropriate path on your VM.

Hit save in the upper right hand corner and close gedit.

<br>
<br>

After verifying the nmap path, subnet range, and changing the output directory to the folder where you want your logs, we will run the script to test it out.

In order to run the script we have to give ourselves execute permissions for the script:
```bash
chmod 700 host-scan-script-nmap.sh
```

<img width="2842" height="214" alt="image" src="https://github.com/user-attachments/assets/75ed7206-9e2b-4583-9122-3be342479276" />

<br>
<br>

Now check to make sure the permissions are set to give you execute permissions. You should notice two things. The color of the file should be green and the the first three fields of the permissions should be set to rwx.
```bash
ls -l
```

<img width="2296" height="264" alt="image" src="https://github.com/user-attachments/assets/241f5150-1823-4951-bb43-64a8431779f5" />

<br>
<br>

now run the script:
```bash
./host-scan-script-nmap.sh
```
The first part of the output should look something like this. 

<img width="2850" height="844" alt="image" src="https://github.com/user-attachments/assets/218f8fc1-0426-414d-8cab-286f55994082" />

The rest should contain a list of the active hosts on your network identified by their IP addresses and Mac addresses. I have not included the portion listing my network devices as a security best practice. 

<br>
<br>

Now switch to the output directory you have set the script to output logs to. For me that folder path is `~/Desktop/Network-Mapping/network-scan-logs`. 

<img width="2252" height="218" alt="image" src="https://github.com/user-attachments/assets/70093b9f-d6fb-4f4d-9ce6-3473516ddf17" />

The website should be populated with 3 files: a .txt file, a .xml file and a .gnmap file.
Note: you can save these various output files when running nmap by using `-oN` with extension `.txt`, `-oX` with extension `.xml`, `-oG` with extension .gnmap, or `-oA` with no extension for all all three formats in three separate files.

Take a look at each file to examine the output. .gnmap is a simplified grepable file with ip addresses. .txt file resembles exactly what you see in your terminal and .xml is the best file to use with scripts and automated processes.

<br>
<br>

Now we will create a script that will take the output of our nmap scan and compare it to the content in our baseline endpoints.csv file. It will tell us if any unrecognized devices appear on our network. 
```bash
gedit compare_network_scan.sh &
```

Now paste the following script into the file:

```bash
#!/usr/bin/env bash

# compare_network_scan.sh
#
# Finds the newest Nmap XML scan, compares its discovered MAC addresses
# against an approved endpoints.csv baseline, and writes a concise report.
#
# Exit codes:
#   0 = All identifiable devices are approved
#   1 = Unknown or unverifiable devices were detected
#   2 = Script/configuration error

set -u
set -o pipefail

###########################################################
# CONFIGURATION — UPDATE THESE PATHS
###########################################################

BASELINE_CSV="${BASELINE_CSV:-$HOME/Desktop/Network-Mapping/text-notes/endpoints.csv}"

XML_SCAN_DIR="${XML_SCAN_DIR:-$HOME/Desktop/Network-Mapping/network-scan-logs}"

REPORT_DIR="${REPORT_DIR:-$HOME/Desktop/Network-Mapping/comparison-reports}"

###########################################################
# SETUP
###########################################################

mkdir -p "$REPORT_DIR" || {
    echo "Error: Could not create report directory: $REPORT_DIR" >&2
    exit 2
}

timestamp=$(date '+%Y-%m-%d_%H-%M-%S')
report_file="$REPORT_DIR/network-comparison-$timestamp.log"
latest_report="$REPORT_DIR/latest-network-comparison.log"

###########################################################
# VALIDATE BASELINE
###########################################################

if [[ ! -f "$BASELINE_CSV" ]]; then
    echo "ERROR: Baseline CSV was not found: $BASELINE_CSV" |
        tee "$report_file" >&2
    exit 2
fi

if [[ ! -r "$BASELINE_CSV" ]]; then
    echo "ERROR: Baseline CSV is not readable: $BASELINE_CSV" |
        tee "$report_file" >&2
    exit 2
fi

###########################################################
# FIND THE NEWEST XML SCAN
###########################################################

latest_xml=$(
    find "$XML_SCAN_DIR" \
        -maxdepth 1 \
        -type f \
        -name '*.xml' \
        -printf '%T@ %p\n' 2>/dev/null |
    sort -nr |
    head -n 1 |
    cut -d' ' -f2-
)

if [[ -z "$latest_xml" ]]; then
    {
        echo "STATUS: ERROR"
        echo "No Nmap XML scan files were found."
        echo "Scan directory: $XML_SCAN_DIR"
    } | tee "$report_file" >&2

    ln -sfn "$(basename "$report_file")" "$latest_report"
    exit 2
fi

###########################################################
# COMPARE THE CSV AND XML
###########################################################

python3 - "$BASELINE_CSV" "$latest_xml" "$report_file" <<'PYTHON'
import csv
import re
import sys
import xml.etree.ElementTree as ET
from datetime import datetime
from pathlib import Path


baseline_path = Path(sys.argv[1])
xml_path = Path(sys.argv[2])
report_path = Path(sys.argv[3])


def normalize_mac(value: str) -> str:
    """Normalize a MAC address into lowercase colon-separated form."""
    value = value.strip().lower().replace("-", ":")
    return value


def valid_mac(value: str) -> bool:
    """Return True when value resembles a standard six-byte MAC address."""
    return bool(
        re.fullmatch(
            r"[0-9a-f]{2}(:[0-9a-f]{2}){5}",
            value,
        )
    )


approved_by_mac = {}
baseline_errors = []

try:
    with baseline_path.open(
        mode="r",
        encoding="utf-8-sig",
        newline="",
    ) as baseline_file:
        reader = csv.DictReader(baseline_file)

        required_columns = {
            "device_name",
            "ip_address",
            "mac_address",
        }

        actual_columns = set(reader.fieldnames or [])

        missing_columns = required_columns - actual_columns

        if missing_columns:
            missing = ", ".join(sorted(missing_columns))
            print(
                f"Baseline CSV is missing required column(s): {missing}",
                file=sys.stderr,
            )
            sys.exit(2)

        for line_number, row in enumerate(reader, start=2):
            device_name = (row.get("device_name") or "").strip()
            baseline_ip = (row.get("ip_address") or "").strip()
            mac = normalize_mac(row.get("mac_address") or "")

            if not mac:
                baseline_errors.append(
                    f"Line {line_number}: {device_name or 'Unnamed device'} "
                    "has no MAC address."
                )
                continue

            if not valid_mac(mac):
                baseline_errors.append(
                    f"Line {line_number}: Invalid MAC address '{mac}' "
                    f"for {device_name or 'Unnamed device'}."
                )
                continue

            if mac in approved_by_mac:
                existing_name = approved_by_mac[mac]["device_name"]
                baseline_errors.append(
                    f"Line {line_number}: Duplicate MAC address {mac} "
                    f"is assigned to both '{existing_name}' and "
                    f"'{device_name}'."
                )
                continue

            approved_by_mac[mac] = {
                "device_name": device_name or "Unnamed approved device",
                "baseline_ip": baseline_ip or "-",
            }

except OSError as error:
    print(f"Could not read baseline CSV: {error}", file=sys.stderr)
    sys.exit(2)


try:
    tree = ET.parse(xml_path)
    root = tree.getroot()
except ET.ParseError as error:
    print(
        f"Could not parse Nmap XML file '{xml_path}': {error}",
        file=sys.stderr,
    )
    sys.exit(2)
except OSError as error:
    print(f"Could not read Nmap XML file: {error}", file=sys.stderr)
    sys.exit(2)


approved_devices = []
unknown_devices = []
unverified_devices = []

for host in root.findall("host"):
    status_element = host.find("status")

    if (
        status_element is not None
        and status_element.get("state") != "up"
    ):
        continue

    ipv4 = "-"
    mac = ""
    vendor = "-"

    for address in host.findall("address"):
        address_type = address.get("addrtype")
        address_value = address.get("addr", "").strip()

        if address_type == "ipv4":
            ipv4 = address_value

        elif address_type == "mac":
            mac = normalize_mac(address_value)
            vendor = address.get("vendor", "-").strip() or "-"

    hostname = "-"

    hostname_element = host.find("./hostnames/hostname")

    if hostname_element is not None:
        hostname = hostname_element.get("name", "-").strip() or "-"

    device = {
        "ip": ipv4,
        "mac": mac or "-",
        "hostname": hostname,
        "vendor": vendor,
    }

    if not mac:
        unverified_devices.append(device)
        continue

    if mac in approved_by_mac:
        approved = approved_by_mac[mac]

        device["device_name"] = approved["device_name"]
        device["baseline_ip"] = approved["baseline_ip"]

        approved_devices.append(device)
    else:
        unknown_devices.append(device)


alert_detected = bool(
    unknown_devices
    or unverified_devices
    or baseline_errors
)

status = "ALERT" if alert_detected else "NORMAL"

report_lines = [
    f"STATUS: {status}",
    f"Comparison time: {datetime.now().astimezone().isoformat(timespec='seconds')}",
    f"Baseline file: {baseline_path}",
    f"Nmap XML file: {xml_path}",
    "",
]

if status == "NORMAL":
    report_lines.extend(
        [
            "All scanned devices with detectable MAC addresses "
            "are present in the approved baseline.",
            f"Approved devices detected: {len(approved_devices)}",
        ]
    )

else:
    if unknown_devices:
        report_lines.extend(
            [
                "UNKNOWN DEVICES:",
                "The following MAC addresses are not present in "
                "the approved baseline:",
            ]
        )

        for device in unknown_devices:
            report_lines.append(
                "  - "
                f"IP: {device['ip']} | "
                f"MAC: {device['mac']} | "
                f"Hostname: {device['hostname']} | "
                f"Vendor: {device['vendor']}"
            )

        report_lines.append("")

    if unverified_devices:
        report_lines.extend(
            [
                "UNVERIFIED DEVICES:",
                "Nmap detected the following active hosts but did "
                "not provide their MAC addresses:",
            ]
        )

        for device in unverified_devices:
            report_lines.append(
                "  - "
                f"IP: {device['ip']} | "
                f"Hostname: {device['hostname']}"
            )

        report_lines.extend(
            [
                "",
                "Because these hosts have no MAC address in the XML scan, "
                "the script cannot determine whether they are approved.",
                "",
            ]
        )

    if baseline_errors:
        report_lines.append("BASELINE WARNINGS:")

        for error in baseline_errors:
            report_lines.append(f"  - {error}")

        report_lines.append("")

report_path.write_text(
    "\n".join(report_lines).rstrip() + "\n",
    encoding="utf-8",
)

print("\n".join(report_lines))

sys.exit(1 if alert_detected else 0)
PYTHON

comparison_exit_code=$?

###########################################################
# MAINTAIN A LINK TO THE NEWEST REPORT
###########################################################

ln -sfn "$(basename "$report_file")" "$latest_report"

case "$comparison_exit_code" in
    0)
        exit 0
        ;;

    1)
        exit 1
        ;;

    *)
        {
            echo
            echo "STATUS: ERROR"
            echo "The comparison could not be completed."
        } >> "$report_file"

        exit 2
        ;;
esac
```

Again you will immediately know from the comments that there are three things that need to be verified or changed before running this script.
- BASELINE_CSV
- XML_SCAN_DIR
- REPORT_DIR
These 3 variables contain directory paths. The first one, BASELINE_CSV, points to the endpoints.csv file that we exported from PostgreSQL with a list of our endpoint devices. <br>
The second, XML_SCAN_DIR, points to the directory path that the nmap script is saving its output to. <br>
The third, REPORT_DIR, is the file where this script will save its output to.

Once more you will need to update the execute permissions for this new script:
```bash
chmod 700 compare_network_scan.sh
```

Now run the script to test it out:
```bash
./compare_network_scan.sh
```

<img width="2880" height="1226" alt="Screenshot 2026-07-26 154744" src="https://github.com/user-attachments/assets/dde9b2f7-f6fa-4762-a1c3-6e5c1eb45744" />

Your output should look similar. As usual I have redacted some of the information as a security best practice. 

<br>
<br>

Now check the folder where you have set this script to output its reports. For me that folder is `~/Desktop/Network-Mapping/comparison-reports`. <br>
In this folder you will notice that there are two files. One will be colored blue, the other will be black like a regular file. At the moment they both contain the exact same content. In the future, every time this script runs a new file will be created. The regular black file that is there now will be old and a new file will be added to the folder. The blue file will always point to whichever file is newest in this folder.

<img width="2588" height="150" alt="image" src="https://github.com/user-attachments/assets/9e4efbec-a049-4efe-bc03-3ab4f13b1e2e" />

You can take a look in either one of these to see the contents, but it should be the exact same thing you saw in the terminal. <br>
In my output you will notice that there are two unrecognized devices. I know that one is my VM as it is listed as “Home-Lab”. The other I know is my phone. I have left these to show how the output of the script may vary. 

<br>
<br>

### Automate these scripts to run with crontab

The first thing you want to do is change the Time Zone of your VM to match your location. 

Check your current settings:
```bash
timedatectl
```
<img width="2098" height="556" alt="Screenshot 2026-07-26 202604" src="https://github.com/user-attachments/assets/5f407408-6f18-4405-8de5-a0eda94b4102" />

<br>
<br>

List available time zones
```bash
timedatectl list-timezones
```
<img width="816" height="1470" alt="image" src="https://github.com/user-attachments/assets/de513e5c-24a8-41f4-91ec-3f9ce528892f" />

<br>
<br>

You can also narrow it down by looking for your specific time zone:
```bash
timedatectl list-timezones | grep New_York
```
<img width="2100" height="218" alt="image" src="https://github.com/user-attachments/assets/3c767a35-7724-4096-aa10-fbc2df6f688a" />

You can reference the following list to help you narrow down which city is associated with your timezone. 
- Central Time (CT)
  - Chicago, Illinois
- Mountain Time (MT)
  - Denver, Colorado
- Pacific Time (PT)
  - Los Angeles, California
- Alaska Time (AK)
  - Anchorage, Alaska
- Hawaii-Aleutian Time (HA)
  - Honolulu, Hawaii

<br>
<br>

Set the time zone:
```bash
sudo timedatectl set-timezone America/New_York
```
The timezone in the upper right hand corner should change. If not, try clicking on it and it should refresh. 

Verify it worked:
```bash
timedatectl
```
<img width="2198" height="554" alt="image" src="https://github.com/user-attachments/assets/e93e71ec-fe41-4349-a42e-428367d4c83a" />

<br>
<br>

We will be using cron to automate the two scripts we created. Open cron:
```bash
crontab -e
```
<img width="2186" height="816" alt="image" src="https://github.com/user-attachments/assets/0149360b-d452-4391-a249-b14a3405a91e" />

The first time you run it, Linux may ask which editor you'd like to use (nano, vim, etc.).

Type `2` to select nano and hit enter.

<br>
<br>

You should see:

<img width="2878" height="1618" alt="Screenshot 2026-07-25 225254" src="https://github.com/user-attachments/assets/b8b18d23-044d-4744-be0c-11936b0a150e" />

<br>
<br>

You can leave the comments there for now. Scroll down using the down arrow key until you reach the bottom and then hit enter a few times just to create some space.

```bash
#!/usr/bin/env bash
# host-scan-script-nmap.sh
# Runs an Nmap host-discovery scan and saves the results in greppable format.
# The script may be executed manually or scheduled through cron.

#########################################
#  THIS AREA MAY REQUIRE MODIFICATION
#########################################
# Replace this with your LAN/subnet if desired
SUBNET="${SUBNET:-192.168.1.0/24}"

#########################################
#  THIS AREA WILL REQUIRE MODIFICATION
#########################################
# Output directory (will be created if missing)
OUTDIR="${OUTDIR:-$HOME/Desktop/Github/Network_Mapping/Host_Scans}"
mkdir -p "$OUTDIR"

#########################################
#  THIS AREA MAY REQUIRE MODIFICATION
#########################################
# Absolute path to the Nmap executable.
# Verify with: which nmap
NMAP="/usr/bin/nmap"

# --- PREPARE OUTPUT FILE (single greppable file) ---
TIMESTAMP="$(date +%Y%m%d-%H%M%S)"
OUTFILE="$OUTDIR/nmap-scan-${TIMESTAMP}.gnmap"

# Use sudo if it is available without a password prompt;
# otherwise run Nmap as the current user.
if sudo -n true 2>/dev/null; then
  SUDO="sudo"
else
  SUDO=""
fi

# <<< ADDED: print a clear timestamped header so cron.log is easy to read
echo "=== [$(date '+%F %T %Z')] Running Nmap scan (output: $OUTFILE) ==="
# <<< END ADDED

# Run nmap host discovery with greppable output only (-sn = ping/host discovery).
${SUDO} "$NMAP" -sn "$SUBNET" -oG "$OUTFILE"
RC=$?

# <<< ADDED: print a footer summarizing result and exit code for easy log parsing
echo "=== [$(date '+%F %T %Z')] Nmap finished (exit code: $RC) ==="
# <<< END ADDED

exit $RC
```










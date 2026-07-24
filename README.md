# networking-mapping-updates

## create database

### 1. Verify the PostgreSQL service is running.
```bash
sudo systemctl status postgresql
```
You want to see: 
Active: active (exited)
or
Active: active (running)

<img width="2008" height="408" alt="Screenshot 2026-07-23 124209" src="https://github.com/user-attachments/assets/66e7ad2f-b97c-4760-b5b8-777b073fdf35" />

<br>
<br>

Get a Simpler Answer
```bash
systemctl is-active postgresql
```
you want to see: 
active

<img width="1118" height="96" alt="Screenshot 2026-07-23 124411" src="https://github.com/user-attachments/assets/e10a8e04-ed4f-4de1-bd56-494a26c6bf4c" />

<br>
<br>
   
To check whether PostgreSQL starts automatically when the VM boots:
```bash
systemctl is-enabled postgresql
```

you want to see:
enabled

<img width="1214" height="94" alt="Screenshot 2026-07-23 124421" src="https://github.com/user-attachments/assets/aff20068-269b-4d17-a147-eb82dfc57286" />

<br>
<br>
   
### 2. Connect to the PostgreSQL server.
PostgreSQL creates a dedicated Linux user named postgres. This user owns the database server and has administrative privileges within PostgreSQL, similar to how root administers Linux.

Rather than connecting directly as your normal Linux account, we'll switch to the postgres user and launch PostgreSQL's interactive command-line client, psql.
```bash
sudo -u postgres psql
```
  - Breaking down the command
    - sudo — execute a command with elevated privileges.
    - -u postgres — run the command as the Linux user postgres.
    - psql — start the PostgreSQL interactive shell.

If everything works, your prompt should change to something like:
postgres=#

<img width="1180" height="248" alt="Screenshot 2026-07-23 124450" src="https://github.com/user-attachments/assets/ae8597a9-d505-47b9-afb1-a8fdf1663dee" />

<br>
<br>

Verify You're Connected
```bash
SELECT version();
```

You should see output similar to:
PostgreSQL 10.18 on x86_64-pc-linux-gnu...


<img width="2820" height="266" alt="Screenshot 2026-07-23 130427" src="https://github.com/user-attachments/assets/63d1d572-fb32-4310-b6b3-151b302e051f" />

<br>
<br>

Next:
```bash
SELECT current_user;
```
 
<img width="670" height="280" alt="Screenshot 2026-07-23 130620" src="https://github.com/user-attachments/assets/203546ee-0249-4e02-aeb8-6e5e692d6c23" />

<br>
<br>

Finally, list the existing databases:
```bash
\l
```
or
```bash
\list
```

<img width="1926" height="492" alt="Screenshot 2026-07-23 130801" src="https://github.com/user-attachments/assets/e8ca5542-f69d-4b06-96c1-b64d65e22bc5" />

These are the default databases PostgreSQL creates during installation.

<br>
<br>


### 3. Create a database named network_inventory.
create the database with:
```bash
CREATE DATABASE network_inventory;
```

<img width="1060" height="98" alt="Screenshot 2026-07-23 134350" src="https://github.com/user-attachments/assets/44de2733-005d-479f-b753-6a0beb032d09" />

<br>
<br>

Verify it was created
```bash
\l
```

<img width="2018" height="532" alt="Screenshot 2026-07-23 134546" src="https://github.com/user-attachments/assets/6ba9175f-a9cb-488d-be19-179a9524607d" />

<br>
<br>

You can also check for that specific database using:
```bash
SELECT datname FROM pg_database WHERE datname = 'network_inventory';
```


<img width="1810" height="244" alt="Screenshot 2026-07-23 135941" src="https://github.com/user-attachments/assets/d94e7873-c22e-4e34-a576-a4857c4b9514" />

<br>
<br>

Connect to the new database
```bash
\c network_inventory
```

Your prompt should  change to "network_inventory=#"

<img width="1628" height="166" alt="Screenshot 2026-07-23 141003" src="https://github.com/user-attachments/assets/8065d52e-4bca-46a6-aab7-7ae0d099c4e0" />

<br>
<br>

Confirm the current connection
```bash
SELECT current_database();
```


<img width="1086" height="228" alt="Screenshot 2026-07-23 141210" src="https://github.com/user-attachments/assets/ad423b39-84cc-484e-b2d7-c533d818ebe2" />

<br>
<br>

### 4. Design and create the endpoints table.

For the first version, we’ll create one practical table that stores each device’s identity, network placement, and tracking information.
```sql
CREATE TABLE endpoints (
    endpoint_id SERIAL PRIMARY KEY,
    device_name VARCHAR(100) NOT NULL,
    hostname VARCHAR(100),
    device_type VARCHAR(50),
    manufacturer VARCHAR(100),
    model VARCHAR(100),
    operating_system VARCHAR(100),
    ip_address INET,
    mac_address MACADDR,
    network_name VARCHAR(100),
    wifi_band VARCHAR(20),
    status VARCHAR(20) DEFAULT 'active',
    first_seen TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_seen TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    notes TEXT
);
```

<img width="1536" height="896" alt="Screenshot 2026-07-23 143355" src="https://github.com/user-attachments/assets/3634ae0e-3e81-4a45-94b6-45722bf0a778" />

<br>
<br>

Verify the table by listing the tables in the current database:
```bash
\dt
```

You should see an entry named: endpoints

<img width="884" height="298" alt="Screenshot 2026-07-23 183605" src="https://github.com/user-attachments/assets/0f265162-f47a-43ab-8ef0-f2ad3af2d75c" />

<br>
<br>

Then inspect its structure:
```bash
\d endpoints
```

<img width="2640" height="978" alt="Screenshot 2026-07-23 184000" src="https://github.com/user-attachments/assets/cd1f1623-ffa0-4219-ade5-2d499a7c2585" />

<br>
<br>

This will show the columns, data types, defaults, and primary key.

<br>
<br>

### 5. Populate it with the endpoint information from your home network.
Insert an endpoint device by running:

```sql
INSERT INTO endpoints (
    device_name,
    hostname,
    device_type,
    manufacturer,
    model,
    operating_system,
    ip_address,
    mac_address,
    network_name,
    wifi_band,
    status,
    notes
)
VALUES (
    'replace this with your device name',
    'replace this with your hostname',
    'replace this with your device type',
    'replace this with your manufacturer',
    'replace this with your model',
    'replace this with your operating_system',
    'replace this with your IP e.g. 255.255.255.255',
    'replace this with your e.g. AA:BB:CC:DD:EE:FF',
    'replace this with your network name',
    'replace this with your wifi_band 5 GHz',
    'replace this with your status',
    'replace this with your notes.'
);
```
Remember to replace the values with your actual endpoint device. Start with you router.

You should receive: 
INSERT 0 1

Now display the table. Because the output may be wide, PostgreSQL might display it awkwardly. You can enable expanded display first. Expanded display shows one column per line, which is easier to read. Turn on expanded display by running:
```bash
\x
```
The same command turns it back off if you type it again later. 

Display the table with:
```bash
SELECT * FROM endpoints;
```
You should see the table appear line by line. The first two lines should resemble: 

<img width="2196" height="94" alt="Screenshot 2026-07-24 093035" src="https://github.com/user-attachments/assets/1a7102e8-44d0-42cb-bbdf-af3dceb92ad3" />

$\color{red}{\text{There is intentionally no screenshot of the full table. It is a good security practice not to share the layout of your home network.}}$

<br>
<br>

Now repeat the process from above where we inserted an endpoint until all of your devices populate the table:

```sql
INSERT INTO endpoints (
    device_name,
    hostname,
    device_type,
    manufacturer,
    model,
    operating_system,
    ip_address,
    mac_address,
    network_name,
    wifi_band,
    status,
    notes
)
VALUES (
    'replace this with your device name',
    'replace this with your hostname',
    'replace this with your device type',
    'replace this with your manufacturer',
    'replace this with your model',
    'replace this with your operating_system',
    'replace this with your IP e.g. 255.255.255.255',
    'replace this with your e.g. AA:BB:CC:DD:EE:FF',
    'replace this with your network name',
    'replace this with your wifi_band 5 GHz',
    'replace this with your status',
    'replace this with your notes.'
);
```

If you make a mistake at any point you can correct it by running something along the lines of:
```bash
UPDATE endpoints
SET
    mac_address = 'corrected mac',
    model = 'corrected model',
    operating_system = 'corrected OS',
    notes = NULL
WHERE endpoint_id = 1;
```

A few things to note:
- You can change multiple lines by separating them with commas.
- the last line before the "WHERE" clause does not need a comma after it.
- I have been separating each part of the command by hitting enter to move to a new line but the command can be executed as a continuous string.
- NULL does not need single quotes around it. Either way works because the program will not execute until it sees a simi-colon. 
- if you put a single quote inside of single quotes you must use two single quotes e.g. 'Wife''s laptop'.
- if you make a mistake and move to the next line you can click ctrl C (on windows) to cancel the command and restart.
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## SCRATCH PAD

---------------------------------------------------------
---------------------------------------------------------
```bash
INSERT INTO endpoints (
    device_name,
    hostname,
    device_type,
    manufacturer,
    model,
    operating_system,
    ip_address,
    mac_address,
    network_name,
    wifi_band,
    status,
    notes
)
VALUES (
    '_______________',
    'hostname',
    '_________________',
    '__________________',
    'CR1000B',
    'operating_system',
    '________________',
    '________________',
    'network name',
    ' wifi_band',
    'status',
    'notes.'
);
```


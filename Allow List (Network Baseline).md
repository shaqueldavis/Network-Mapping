# Creating list of allowed endpoints on network

This Phase of the project requires that you first complete Phase 1 as outlined in the file "Endpoint Database"

## Create document
If you are still connected to the network_invitory database from the last phase you will need to exit back to the terminal with:
```SQL
\q
```

<img width="540" height="92" alt="Screenshot 2026-07-24 144144" src="https://github.com/user-attachments/assets/2284bed1-c6e6-4a3b-b163-6d2c09f36301" />

<br>
<br>


When we connected to the database earlier we connected as the postgres user for PostgresSQL. This is essentially the root user for the PostgresSQL. 

The reason we initially connected as the PostgreSQL root user is because we were setting up and administering PostgreSQL, not just using it. Connecting as the PostgreSQL superuser is the standard way to perform the initial setup.

Our next step involves copying information from the database table we created earlier and placing it in a new file within our local users directory path.

In order to do this we must grant our local user permission to the database.

create a PostgreSQL role named “your username here”.

First connect back to the PostgreSQL server as the postgres user:
```bash
sudo -u postgres psql
```

<img width="1034" height="150" alt="image" src="https://github.com/user-attachments/assets/de5bf945-509d-4927-8092-cdae06b1d2d1" />

<br>
<br>

At the postgres=# prompt, create the role:
```sql
CREATE ROLE shaquel WITH LOGIN;
```
remember to replace "shaquel" with your username.

<img width="918" height="112" alt="image" src="https://github.com/user-attachments/assets/f2e2c429-df1c-4038-ab82-5ff27a06538a" />

<br>
<br>

Then allow that role to connect to the network_inventory database:
```sql
GRANT CONNECT ON DATABASE network_inventory TO shaquel;
```
remember to replace "shaquel" with your username.

<img width="1468" height="90" alt="image" src="https://github.com/user-attachments/assets/8df3d5f3-a831-450a-afa9-a402781e83b2" />

<br>
<br>

Switch into the database:
```sql
\c network_inventory
```

<img width="1582" height="140" alt="image" src="https://github.com/user-attachments/assets/efa9d169-62b4-4c9c-8f0d-232dbf6ae702" />

<br>
<br>

Grant access to the existing schema and tables:
```sql
GRANT USAGE ON SCHEMA public TO shaquel;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO shaquel;
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO shaquel;
```

<img width="2140" height="266" alt="image" src="https://github.com/user-attachments/assets/485a9035-1a6f-4abc-b7f0-9371d46ab5a5" />

<br>
<br>

Then exit:
```sql
\q
```

<img width="592" height="88" alt="image" src="https://github.com/user-attachments/assets/9b5bdd29-ae70-4bbf-9156-2ef735c67367" />

<br>
<br>

Now connect normally as your Linux user:
```bash
psql -d network_inventory
```

<img width="1042" height="158" alt="image" src="https://github.com/user-attachments/assets/575f67e5-6e28-427e-a094-aebd61183791" />

you should get a prompt that looks like: network_inventory=>

notice the change from # to > at the end of the prompt. that is because you essentially went from the root user to a normal user.

<br>
<br>

Confirm both identities. That of your current database user and linux user:
```sql
SELECT current_user;
\! whoami
```

<img width="932" height="454" alt="image" src="https://github.com/user-attachments/assets/7dd5d218-40b0-4c3f-857c-a0ab35568e96" />

It will say your username in place of "shaquel"

<br>
<br>

now we will run the command needed to create a file in your local directory with a list of our device names, IP addresses, and MAC addresses:

```bash
\copy (SELECT device_name, ip_address, mac_address FROM endpoints WHERE status = 'active' AND ip_address IS NOT NULL ORDER BY ip_address) TO '/home/shaquel/Desktop/Network-Mapping/text-notes/endpoints.csv' WITH (FORMAT csv, HEADER);
```

<img width="2880" height="288" alt="image" src="https://github.com/user-attachments/assets/26166d83-b59a-4c8d-967f-e062623079a9" />

the portion of the command between the single quotes should be replaced with your path of choice on your VM.



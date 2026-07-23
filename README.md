# networking-mapping-updates

## create database

### 1. Verify the PostgreSQL service is running.
- run command
  - sudo systemctl status postgresql
    - You want to see: Active: active (exited)
    - or
    - Active: active (running)

<img width="2008" height="408" alt="Screenshot 2026-07-23 124209" src="https://github.com/user-attachments/assets/66e7ad2f-b97c-4760-b5b8-777b073fdf35" />

<br>
<br>

Get a Simpler Answer
- run command
  - systemctl is-active postgresql
    - you want to see: active

<img width="1118" height="96" alt="Screenshot 2026-07-23 124411" src="https://github.com/user-attachments/assets/e10a8e04-ed4f-4de1-bd56-494a26c6bf4c" />

<br>
<br>
   
To check whether PostgreSQL starts automatically when the VM boots:
- run command
  - systemctl is-enabled postgresql
  - you want to see: enabled

<img width="1214" height="94" alt="Screenshot 2026-07-23 124421" src="https://github.com/user-attachments/assets/aff20068-269b-4d17-a147-eb82dfc57286" />

<br>
<br>
   
### 2. Connect to the PostgreSQL server.
PostgreSQL creates a dedicated Linux user named postgres. This user owns the database server and has administrative privileges within PostgreSQL, similar to how root administers Linux.

Rather than connecting directly as your normal Linux account, we'll switch to the postgres user and launch PostgreSQL's interactive command-line client, psql.
- run command
  - sudo -u postgres psql
    - Breaking down the command
      - sudo — execute a command with elevated privileges.
      - -u postgres — run the command as the Linux user postgres.
      - psql — start the PostgreSQL interactive shell.

If everything works, your prompt should change to something like:
- postgres=#

<img width="1180" height="248" alt="Screenshot 2026-07-23 124450" src="https://github.com/user-attachments/assets/ae8597a9-d505-47b9-afb1-a8fdf1663dee" />

<br>
<br>

Verify You're Connected
- run command
  - SELECT version();

Once you're at the postgres=# prompt, run:
### 3. Create a database named network_inventory.



### 4. Design and create the endpoints table.

### 5. Populate it with the endpoint information you've already been documenting from your home network.





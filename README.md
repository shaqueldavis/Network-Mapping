# networking-mapping-updates

## create database

### 1. Verify the PostgreSQL service is running.
- run command
  - sudo systemctl status postgresql
    - You want to see: Active: active (exited)
    - or
    - Active: active (running)

Get a Simpler Answer
- run command
  - systemctl is-active postgresql
    - you want to see: active
   
To check whether PostgreSQL starts automatically when the VM boots:
- run command
  - systemctl is-enabled postgresql
  - you want to see: enabled

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
  

### 3. Create a database named network_inventory.

### 4. Design and create the endpoints table.

### 5. Populate it with the endpoint information you've already been documenting from your home network.





# How to repair the pihole-FTL.db

If the pihole-FTL.db becomes corrupted follow the steps below to try and recover the database  

## Create an sqlite3 container

Create an image with the necessary binary *(sqlite3)* so that you don't have to install sqlite3 locally

### Sqlite3 Docker Image Build Steps

Create a docker file to run sqlite3 from a container  
> This is done so that you don't have to install anything locally :wink:  

- Create a file named `Dockerfile`  

```Dockerfile
FROM arm64v8/alpine:latest
RUN apk add --no-cache sqlite
ENTRYPOINT ["sqlite3"]
```

- Build the container  
`sudo docker build -t arm-sqlite .`

## Make a copy of the database

We will make a copy of the pihole-FTL.db before attempting any fix on the production database *(the one that is in use by pihole)*  

### Stop the pihole container

- Navigate to the directory containing the *docker-compose.yml* file  
  - `sudo docker-compose down`

### Backup the database

- Copy the database to a directory  
  - `cp ./etc-pihole/pihole-FTL.db ./backups/sql/pihole-FTL.db`

## Check the database integrity

- Run the following command to check the integrity of the database  
  - `sudo docker run --rm -v /home/alykes/backups/sql:/db arm-sqlite /db/pihole-FTL.db "PRAGMA integrity_check;"`  

- You will get something like the following:  

```bash
*** in database main ***
Tree 2 page 332 cell 85: 2nd reference to page 62256
Tree 2 page 332 cell 84: Rowid 16681137 out of order
Tree 4 page 1232 cell 35: 2nd reference to page 62259
Error: stepping, database disk image is malformed (11)
```

## Get an SQL dump of the corrupted database

We will perform an SQL dump of the corrupted database so that we can attempt to rebuild it  

- Run the following command to get an SQL dump of the database  
  - `sudo docker run --rm -v /home/alykes/backups/sql:/db arm-sqlite /db/pihole-FTL.db .dump > /home/alykes/backups/sql/pihole-FTL.sql`

> Expect the file to be larger than the original pihole-FTL.db size. We will use this file to construct a new database
> Use `watch ls -alh` if you would like to see the progress of the file being generated

## Create a new database file

To create a new database file, we will need to run sqlite3 and perform some commands at the `sqlite>` prompt

- Run the container interactively
  - `sudo docker run -it --rm -v /home/alykes/backups/sql:/db arm-sqlite /bin/bash`
- At the prompt enter the following commands  
  - Open a database for writing  
    - `.open /db/new-sql.db`
    - You may need to perform `sudo chmod 777 /home/alykes/backups/sql` if you receive a permission error as shown here `/db/new-sql.db": unable to open database file`
    - This process may take a while :coffee:
  - To see that you have the database selected enter  
    - `.databases` - You should see the following line `main: /db/slq.db r/w`
  - Create the new database from the .sql file we created earlier  
    - `.read /db/pihole-FTL.sql` - This process may take a few minutes *(or longer)*, depending on how large your database file is  
  - Check that the new database has contents with either of the following commands  
    - `.tables`
    - `SELECT COUNT(*) FROM queries;`  
  - Exit the sqlite prompt  
    - `.exit`  
- Finally, check the integrity of the new database file with the following command
  - `sudo docker run --rm -v /home/alykes/backups/sql:/db arm-sqlite /db/new-sql.db "PRAGMA integrity_check;"`  

You now have an uncorrupted database file! :trophy:

## Copy the file back into the etc-pihole directory

- Move the existing pihole-FTL.db file  
  - `mv pihole-FTL.db pihole-FTL.corrupt`
- Move the newly created database into etc-pihole  
  - `mv /home/alykes/backups/sql/new-sql.db /home/alykes/etc-pihole/pihole-FTL.db`

## Restart pihole

- Restart pihole and cross your fingers :four_leaf_clover:
  - `sudo docker-compose up --build --detach`

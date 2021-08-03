# PostgreSQL PITR with Pitrery fast configuration

Backup and recovery instructions for postgresql-12 using Pitrery for PITR (Point-in-Time Recovery)

## Steps to reproduce an easy and effective backup and restoration of a cluster

**Requirements**
- PostgreSQL 12
- Pitrery for postgreSQL (installed and configured)
- Some basic knowledge of backup and recovery, and postgresql config manipulation knowledge

First create a base backup using `pitrery backup` with needed options to ensure the backup is well

for example:

`pitrery backup -d some_conf`

Then what you will need is to archive the transactional logs aka the WALs 

`psql -c "select pg_switch_wal();"`

After we have done that we need to stop the service in order to make the restoration in the desired server

`# systemctl stop postgresql@12-main`

Then if there is an existing installation of postgresql in the machine we are going to restore our database cluster 
we should delete the contents of the main folder `/var/lib/postgresql/12/main` we can do that using the following command

`rm -r /var/lib/postgresql/12/main/*`

**Be aware that using `rm` can be dangerous if you are not paying attention to what you are deleting**

normaly after this you would restore the backup by adding a `recovery.signal` file and extracting a tar file with the base backup, but in this case we 
will use pitrery to do that for us.

using the postgres user execute the following command
  
`pitrery restore -d 'YYYY-MM-DD HH:MM:SS'` adding the date point-in-time where we want to restore our database

Then we check first if the `recovery.signal` is correctly configured, in any case you can add the instructions manually 
in case it is not configured.

The start the postgresql server and check the logs for prompt

`systemctl start postgresql@12-main && tail -f /var/log/postgresql/postgresql.log`

And that should be it

TODO: Add more detail into each instruction

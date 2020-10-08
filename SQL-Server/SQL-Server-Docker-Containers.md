# NOTE THAT THIS DOCUMENT IS STILL IN THE PROCESS OF BEING WRITTEN

# Running SQL Server in Docker containers
This section will contain the basics and information on how to deploy a SQL-Server instance running on Docker. It is based off the original documenation on Microsoft's site.

# Pre-requisites
Before deploying the server, ensure that you have Docker running and have the following resources made available:

- At least 6GB Disk Space

- At least 1 GB Memory

- An x64 Processor (2.0GHz or faster)

## Installing SQL Server in Docker
`docker pull mcr.microsoft.com/mssql/server`

## Running a basic instance of SQL Server
`docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest --name sql-server`

Alternatively, you could create a Docker-compose file with the following parameters:

```
version: '3'

services:
    sql-server:
        image: mcr.microsoft.com/mssql/server
        environment: 
            - ACCEPT_EULA=Y
            - SA_PASSWORD=Password1234
            - MSSQL_PID=Express
        ports:
            - "1433:1433"
        
```

## Accessing the instance of SQL Server
`docker exec -it sql-server /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P 'Password1234'`

A `1>` appearing on the Command Line will affirm that you have successfully logged into the SQL Server instance. 

To verify that you can query data from the server, simply query the list of available databases by using 

`SELECT name FROM sys.Databases`

Followed by 

`GO`

Note that `GO` is required everytime you wish to read/write/update data in the database.


https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-docker-container-deployment?view=sql-server-ver15&pivots=cs1-bash

https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-docker-container-deployment?view=sql-server-ver15&pivots=cs1-bash#next-steps

https://docs.microsoft.com/en-us/sql/linux/tutorial-restore-backup-in-sql-server-container?view=sql-server-ver15

https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15&pivots=cs1-bash

https://docs.microsoft.com/en-us/dotnet/architecture/microservices/multi-container-microservice-net-applications/database-server-container

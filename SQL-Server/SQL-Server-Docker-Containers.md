# NOTE THAT THIS DOCUMENT IS STILL IN THE PROCESS OF BEING WRITTEN

# Running SQL Server in Docker containers
This section will contain the basics and information on how to deploy a SQL-Server instance running on Docker. It is based off the original documenation on Microsoft's site.

# Pre-requisites
Before deploying the server. I made changes to this line. 

## Installing SQL Server in Docker
`docker pull mcr.microsoft.com/mssql/server`

## Running a basic instance of SQL Server
`docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -e 'MSSQL_PID=Developer' -p 1433:1433 -d mcr.microsoft.com/mssql/server:2019-latest --name sql-server`

## Accessing the instance of SQL Server
`docker exec -it sql-server /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Password1234`



https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-docker-container-deployment?view=sql-server-ver15&pivots=cs1-bash

https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-docker-container-deployment?view=sql-server-ver15&pivots=cs1-bash#next-steps

https://docs.microsoft.com/en-us/sql/linux/tutorial-restore-backup-in-sql-server-container?view=sql-server-ver15

https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15&pivots=cs1-bash

https://docs.microsoft.com/en-us/dotnet/architecture/microservices/multi-container-microservice-net-applications/database-server-container

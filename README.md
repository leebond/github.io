## How to setup and use Oracle Database Enterprise Edition 12c Release 2 in a Docker container on MacOS X

#### Install Docker Desktop 
download and install normally from [docker-mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac)
#### Log in to Docker Desktop via Terminal:
you will be prompted for your docker username and password after running this command

`$ docker login`

#### Pull Oracle Database Enterprise Edition 12cR2 image from Docker hub
next, pull Oracle 12cR2 image using the following 

`$ docker pull store/oracle/database-enterprise:12.2.0.1`

#### Install Oracle sqlplus and Oracle client in macOS
you need Oracle sqlplus and client to connect to the database, follow Steps 1 & 2 from the following guide
[how-to-install-oracle-sqlplus-and-oracle-client-in-mac-os](https://tomeuwork.wordpress.com/2014/05/12/how-to-install-oracle-sqlplus-and-oracle-client-in-mac-os/)

#### Add Oracle client and library to PATH
In Terminal

`$ vi ~/.bash_profile`


Some convenient vi commands

`i` to insert

`:q!` to exit without saving

`:qw` to save and exit

In vi
```markdown
export ORACLE_HOME=/Applications/oracle/product/instantclient_64/12.2.0.1
export PATH=$ORACLE_HOME/bin:$PATH
export DYLD_LIBRARY_PATH=$ORACLE_HOME/lib
```
save and exit vi

`:qw`

In Terminal

`$ source ~/.bash_profile`

#### Now we can setup the connection to Oracle docker container
##### Choice 1: Run container using automatically assigned ports
over here I am giving my oracle container the name "myorcldb"
the following command will run a docker container with Oracle database

`$ docker run -d -it --name myorcldb -P --restart always store/oracle/database-enterprise:12.2.0.1`

wait for a few minutes and check that container status changes from “starting” to “healthy”

`$ docker ps`

##### Choice 2: Run container using manually assigned ports
`$ docker run -d -it --name myorcldb -p <yourip>:<assignedip> --restart always store/oracle/database-enterprise:12.2.0.1`

##### If you need to re-create the container
`$ docker stop <container id>`

`$ docker rm <container id>`

##### Check ports
we need port information so that our Oracle client can listen to the right port that the database has exposed,
run the following to check the port assigned

`$ docker port myorcldb`

since I was automatically assigned to port 32773, I will be using that information in the next step.

### Create a “tnsnames.ora” file 
in the following directory 

`/Applications/oracle/product/instantclient_64/12.2.0.1/network/admin` 

create a tnsnames.ora text file, this file is used by the Oracle client to connect to the database

`$ vi /Applications/oracle/product/instantclient_64/12.2.0.1/network/admin/tnsnames.ora`

copy the contents below into the vi editor
from the "check ports" step we also noticed the ip address was 0.0.0.0 i.e. localhost, hence we will use
localhost in the connection details

Contents of your tnsnames.ora file
```markdown
ORCLPDB1=
(DESCRIPTION=
(ADDRESS=
(PROTOCOL=TCP)
(HOST=localhost)(PORT=32773))
    (CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=ORCLPDB1.localdomain)))
```

and you're all set to start using your database!

### Key in the following login credentials on your DBMS GUI
in sqldeveloper you can key in the following credientials on clicking "New connection"
test the connection before saving

```markdown
Connection name: <any preferred naming>
username: system
password: Oradoc_db1
hostname: localhost
port: 32773
service name: ORCLPDB1.localdomain
```

### Since I personally like using DBeaver
you may download it from [dbeaver.io/download/](https://dbeaver.io/download/)
the credientials are similar but in different fields
test the connection before saving

```markdown
Host: localhost
Port: 32773
Database: ORCLPDB1.localdomain [Service name]
username: system
password: Oradoc_db1
```

### Create a database User 
your database comes clean with no users created, you can create a user account for your specific needs
with the following command by running the below in the DBMS GUI as SYSTEM admin
`CREATE USER student IDENTIFIED BY studentpassword DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;`

### Usage
since the docker container is running backend, stopping the docker container will stop the database service.
removing your docker container means you will lose your database.
hence, do not remove the docker container. If you do so you will have to restart the setup process again.

### References
Oracle Database Server Docker Image Documentation

[run-oracle-database-in-docker-using-prebaked-image-from-oracle-container-registry-a-two-minute-guide](https://technology.amis.nl/2017/11/18/run-oracle-database-in-docker-using-prebaked-image-from-oracle-container-registry-a-two-minute-guide/)

[how-to-install-oracle-sqlplus-and-oracle-client-in-mac-os](https://tomeuwork.wordpress.com/2014/05/12/how-to-install-oracle-sqlplus-and-oracle-client-in-mac-os/)

## How to run and connect to Oracle Database Enterprise Edition 12c Release 2 in a Docker container on MacOS X

#### Install Docker Desktop 
download and install normally from [docker-mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac)
#### Log in to Docker Desktop via Terminal:
you will be prompted to key in your docker username and password
`$ docker login`

#### Pull Oracle Database Enterprise Edition 12cR2 image from Docker hub
`$ docker pull store/oracle/database-enterprise:12.2.0.1`

#### Install oracle sqlplus and oracle client in macOS

[how-to-install-oracle-sqlplus-and-oracle-client-in-mac-os](https://tomeuwork.wordpress.com/2014/05/12/how-to-install-oracle-sqlplus-and-oracle-client-in-mac-os/)

#### Add oracle sqlclient and library to PATH
In Terminal

`vi ~/.bash_profile`


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

### Create a “tnsnames.ora” file 
in the following directory /Applications/oracle/product/instantclient_64/12.2.0.1/network/admin

Contents of your tnsnames.ora file
```markdown
ORCLPDB1=
(DESCRIPTION=
(ADDRESS=
(PROTOCOL=TCP)
(HOST=localhost)(PORT=32773))
    (CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=ORCLPDB1.localdomain)))
```

#### Setup to connect to oracle docker container from OS application
##### Run container using automatically assigned ports
Over here I am giving my oracle container the name `myorcldb`
`docker run -d -it --name myorcldb -P store/oracle/database-enterprise:12.2.0.1`
wait for a few minutes and check that container status changes from “starting” to “healthy”
`docker ps`
##### Run container using manually assigned ports
`docker run -d -it --name myorcldb -p <yourip>:<assignedip> store/oracle/database-enterprise:12.2.0.1`
##### if you need to re-create the container:
`$ docker stop <container id>
docker rm <container id>`
##### check ports
`docker port myorcldb`
the port that i was automatically assigned was 32773, I will need this information in the next step.

### Key in the following log in credentials on your DBMS GUI
```markdown
Connection name: <any preferred name>
username: system
password: Oradoc_db1
hostname: localhost
port: 32773
service name: ORCLPDB1.localdomain
```

### Since I personally like using DBeaver
You may download it from [dbeaver.io/download/](https://dbeaver.io/download/)
```markdown
Host: localhost
Port: 32773
Database: ORCLPDB1.localdomain [Service name]
username: system
password: Oradoc_db1
```

### Create a database User 
`CREATE USER student IDENTIFIED BY studentpassword DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;`

### Usage 
```markdown
Warning: do not remove the docker container - removal will result in environment and data loss. You will have to restart the setup process again.
```

### References
[run-oracle-database-in-docker-using-prebaked-image-from-oracle-container-registry-a-two-minute-guide](https://technology.amis.nl/2017/11/18/run-oracle-database-in-docker-using-prebaked-image-from-oracle-container-registry-a-two-minute-guide/)
[how-to-install-oracle-sqlplus-and-oracle-client-in-mac-os](https://tomeuwork.wordpress.com/2014/05/12/how-to-install-oracle-sqlplus-and-oracle-client-in-mac-os/)

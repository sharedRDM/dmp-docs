# PostgreSQL

**Current version: (PostgreSQL) 12.14 (Debian 12.14-1.pgdg110+1)**

## Install PostgreSQL 12

```bash
sudo apt update && apt upgrade
sudo apt -y install gnupg2

wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" |sudo tee  /etc/apt/sources.list.d/pgdg.list

sudo apt update
sudo apt -y install postgresql-12 postgresql-client-12

# enable & start
systemctl enable postgresql
systemctl start postgresql

# check status
systemctl status postgresql
```

## Configurations (test instance)

### modify configs

#### vi /etc/postgresql/12/main/pg_hba.conf

```bash
local   all             postgres                                md5
local   all		        dmp					                    md5

# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     md5
 
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
host    all             all             <HOST_IP>/32            md5 # allow this host
host    all             all             172.0.0.0/8             md5 # allow docker ip range
host    all             all             0.0.0.0/0               md5 # or everything open
```

#### vi /etc/postgresql/12/main/postgresql.conf

```diff
- #listen_addresses = 'localhost'         # what IP address(es) to listen on;
+ listen_addresses = '<ip addresses>'     # what IP address(es) to listen on;
```

### change postgres user password

```bash
su - postgres
ALTER USER postgres PASSWORD '<PASSWORD>';
```

### create database & user

```sql
CREATE DATABASE "dmp";

-- (dmp) This user will access the database from backend
CREATE USER "dmp" WITH ENCRYPTED PASSWORD '<PASSWORD>';
GRANT ALL PRIVILEGES ON DATABASE "dmp" to "dmp";

ALTER DATABASE dmp OWNER TO dmp;
```

#### test your user
```bash
psql -U dmp
```



## Configurations (prod instance)

### modify configs

#### vi /etc/postgresql/12/main/pg_hba.conf

```bash
local   all             postgres                                md5
local   all		        dmp					                    md5

# Allow external hosts
host    all             all             <HOST-IP>/32         md5 # dmp.tugraz
```

#### vi /etc/postgresql/12/main/postgresql.conf

```diff
- #listen_addresses = 'localhost'         # what IP address(es) to listen on;
+ listen_addresses = '<HOST-IP>'     # what IP address(es) to listen on;
```

### change postgres user password

```bash
su - postgres
ALTER USER postgres 'PASSWORD';
```

### create database & user

```sql
CREATE DATABASE "dmp";

-- (dmp) This user will access the database from backend
CREATE USER "dmp" WITH ENCRYPTED PASSWORD '<PASSWORD>';
GRANT ALL PRIVILEGES ON DATABASE "dmp" to "dmp";

ALTER DATABASE dmp OWNER TO dmp;
```

#### test your user
```bash
psql -U dmp
```

This is an atomic application based on the nulecule specification. Kubernetes and native docker are currently the only supported providers. You'll need to run this from a workstation that has the atomic command and kubectl client that can connect to a kubernetes master.

It's a single container application based on the centos/postgresql image.

### Option 1: Interactive

Run the image. It will automatically use kubernetes as the orchestration provider.  It will prompt for all parameters in the Nulecule file that do not have default values.  These are "db_user", "db_pass", and "db_name"

    $ [sudo] atomic run projectatomic/postgresql-centos7-atomicapp

## Option 2: Unattended

1. Create the file `answers.conf` with these contents:

    This sets up the values for the two configurable parameters (image and hostport) and indicates that kubernetes should be the orchestration provider.

        [general]
        provider = kubernetes

        [postgresql-atomicapp]
        db_user = username
        db_pass = password
        db_name = dbname

1. Run the application from the current working directory

        $ [sudo] atomic run projectatomic/posgtresql-centos7-atomicapp

1. As an additional experiment, remove the kubernetes pod and change the provider to 'docker' and re-run the application to see it get deployed on native docker.

### Option 3: Install and Run

You may want to download the application, review the configuraton and parameters as specified in the Nulecule file, and edit the answerfile before running the application.

1. Download the application files using `atomic run IMAGE --mode fetch`

        [sudo] atomic run projectatomic/postgresql-centos7-atomicapp --mode fetch

2. Rename `answers.conf.sample`

        mv answers.conf.sample answers.conf

3. Edit `answers.conf`, review files if desired and then run

        $ [sudo] atomic run projectatomic/postgresql-centos7-atomicapp

## Test
Any of these approaches should create a kubernetes pod and service.

Let's say, you've created a PostgreSQL database: ``test`` with ``test`` as its
owner, you can test it using the `psql` command in the centos/postgresql
container.

```
$ kubectl get service postgresql
NAME      LABELS    SELECTOR       IP               PORT(S)
postgresql   name=db   name=postgresql   10.254.167.159   5432/TCP

sudo docker run --rm -ti centos/postgresql psql -h 10.254.167.159 -U test -d test -W
Password for user test: <password>
psql (9.2.7)
Type "help" for help.

test=# \l
                             List of databases
   Name    |  Owner   | Encoding  | Collate | Ctype |   Access privileges
-----------+----------+-----------+---------+-------+-----------------------
 postgres  | postgres | SQL_ASCII | C       | C     |
 template0 | postgres | SQL_ASCII | C       | C     | =c/postgres          +
           |          |           |         |       | postgres=CTc/postgres
 template1 | postgres | SQL_ASCII | C       | C     | =c/postgres          +
           |          |           |         |       | postgres=CTc/postgres
 test      | postgres | SQL_ASCII | C       | C     | =Tc/postgres         +
           |          |           |         |       | postgres=CTc/postgres+
           |          |           |         |       | test=CTc/postgres
(4 rows)

test=# \conninfo
You are connected to database "test" as user "test" on host "10.254.167.159" at port "5432".
```

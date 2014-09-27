
# millisami/postgresql

    docker run --rm -P --name postgres millisami/postgresql

## Runs postgres in foreground

    docker run --rm -P --name pg_test millisami/postgresql

## Run with the Volumes mounted to save the database and logs

  First start a data volume container.

    docker run -d -v /dbdata --name dbdata millisami/postgresql echo Data-only container for postgres

    docker run -d --volumes-from dbdata --name db1 millisami/postgresql


```
psql -h localhost -U docker -d docker --password

* \l -> lists the databases
* \c dbname -> changes the current database
* \d -> lists the tables of the current database

CREATE TABLE films (
    code        char(5),
    location    point
);
INSERT INTO films VALUES ('Meor', '(-194.0, 53.0)');
```

    docker run -d --volumes-from dbdata --name db2 millisami/postgresql


### Backup, restore, or migrate data volumes

    docker run --volumes-from dbdata -v $(pwd):/backup busybox tar cvf /backup/backup.tar /dbdata

You could then restore it to the same container, or another that you've made elsewhere. Create a new container.

    docker run -v /dbdata --name dbdata2 busybox /bin/bash

Then un-tar the backup file in the new container's data volume.

    docker run --volumes-from dbdata2 -v $(pwd):/backup busybox tar xvf /backup/backup.tar

## Creating a new database

  First start the container with millisami/postgresql image.

    docker run -it --rm --link db1:pg millisami/postgresql /bin/sh

  Inside the container,  

    createdb -O docker -h $PG_PORT_5432_TCP_ADDR -p $PG_PORT_5432_TCP_PORT -U docker <db_name>

## Linking container and listing the env vars

    docker run -it --rm --link db1:dd busybox env

## Run the rails app:

    docker run -d -p 80:80 --name haloapp --link db1:db -v $(pwd):/app millisami/rails

For the additional postgres command to run against via command line can be found here (Docker postgresql workflow)[http://davidamick.wordpress.com/2014/07/19/docker-postgresql-workflow/]

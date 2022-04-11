# SQL Statements
https://dev.mysql.com/doc/refman/8.0/en/sql-statements.html

## Database Administraction Statements
https://dev.mysql.com/doc/refman/8.0/en/sql-server-administration-statements.html

### Table Maintenance Statements 
https://dev.mysql.com/doc/refman/8.0/en/table-maintenance-statements.html

```mysql
analyze table stock_universe;
check table stock_universe;
checksum table stock_universe;
optimize table stock_universe;
repair table stock_universe;
```

### Set Statements
https://dev.mysql.com/doc/refman/8.0/en/set-statement.html
```mysql
set max_connections = 1000;
```

### Show Statements 
https://dev.mysql.com/doc/refman/8.0/en/show.html

```mysql
show binary logs;

show binlog events;

show character set;

show collation;

show columns from information_schema.TABLES;

show create database information_schema;
# show create event xxx;
# show create procedure xxx;
# show create function xxx;
# show create table xxx;
# show create trigger xxx;
# show create user xxx;
# show create view xxx;

show databases;

show engines;
show engine InnoDB status;
show engine InnoDB mutex;

show errors;

show events;

show function code upper;
show function status;

show grants;

show index from information_schema.TABLES;

show master status;

show open tables;

show plugins;

show privileges;

# show procedure code xxx;
# show procedure status xxx;

show processlist;

show profiles;
# show profile xxx;

show relaylog events;

show replicas;

show slave hosts;

show replica status;
show slave status;

show status;

show tables;
show table status;

show triggers;

show variables;
show variables like 'autocommit';
show variables like '%version';
show variables like 'max_connections';
show variables like '%timeout%';
show variables like '%size%';

show warnings;
```

### Other Statements
https://dev.mysql.com/doc/refman/8.0/en/other-administrative-statements.html

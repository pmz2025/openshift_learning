
## Show databases
mysql -u user1 -p -h localhost -e "show databases;"

## Find the tables inside the database
mysql -u user1 -pmypa55w0rd -h localhost -D items -e "show tables;"

## Query the table inside the database
mysql -u user1 -pmypa55w0rd -h localhost -D items -e "select * from Item;"

> you must know the name of the table

## insert the data using sample_db.sql

mysql -u$MYSQL_USERNAME -p$MYSQL_PASSWORD <databaseName> < locationof.sql file 

mysql -uuser1 -pmypa55w0rd items <sample_db.sql

>note: items a database
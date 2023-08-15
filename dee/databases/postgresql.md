# PostgreSQL

## Notes - LinuxCBT Tutorial

### psql

* Features include
  * command history
  * tab completion
  * commands terminate with semicolons
* `psql --version` prints on the terminal the current version of PostgreSQL
* `psql -l` lists all the available databases and exits
* `psql -U` lets you specify the username (defaults to currently-logged-in user)
* `psql -f <FILENAME>` executes commands from an external file
* PostgreSQL installs 3 default DBs
  * `postgres` - contain user accounts, statistics, and other things
  * `template0` - original DB
  * `template1` - (possibly extended) copy of template0, used to generate new DBs
* `psql` without any arguments enters the interactive mode:
  * `\h` returns a SQL-specific help (`\h COMMAND` will give you documentation for that command)
  * `\?` returns PSQL-specific help
  * `\l` lists all the DBs (`\l+` returns more information)
  * `\du` returns list of users in system (`\du+` for more information)
  * `\!` launches a shell inside sql (`\! COMMAND` will execute the command onto a shell)
  * `\i FILE` executes commands (SQL and sql) from an external file
  * `\c DBNAME` connect to different DB \q quits the database

### Access Control

* You can configure users and roles (roles are really users)
* The configuration files involved with the access control are `pg_hba.conf`, `postgresql.conf`, and `pg_ident.conf` (much less than the other two)
* Accounts are shared among all DBs in PostgreSQL
* Default setup includes 1 superuser called `postgres`
* Privileges are managed with:
  * `GRANT | REVOKE`
  * `ALTER`
  * `CREATE | DROP ROLE|USER`
* The creator of a DB object is its owner, and can assign privileges
* To change the ownership of a DB object, we use the `ALTER` SQL command
* The special `PUBLIC` role assigns privileges to ALL system users
* `\password <USERNAME>` allows to specify a password for the user
* By default, psql tries to connect to a DB with the same name as the username trying to log in
* The postgres user cannot be removed from the system&#x20;
* Default configuration does not allow remote login, because it is not specified in the `pg_hba.conf` file

### Log Configuration

* PostgreSQL supports three kinds of logging:
  * stderr - the default
  * csvlog - import into spreadsheets or DBs for post-processing
  * syslog
* We can write to all these logs simultaneously
* The config file responsible for setting up the log system is `postgresql.conf` (which can be read without root privileges)
* Automatic log rotation is included, based on age or size
* The logs are stored in `%POSTGRESROOT/data/pg_log`
* Each time you (re)start PostgreSQL, it generates a new log file
* Syslog is based on UDP, and so is prone to message loss

### Data Types

* Data types work on a per-column basis
* Types include:
  * Numeric:
    * smallint -16 bits, whole numbers
    * int - 32 bits, whole numbers
    * bigint - 64 bits, whole numbers
    * numeric - with precision and scale
    * real - 32 bits, variable with max 6 decimal digits
    * double precision - 64 bytes, variable with max 15 decimal digits
    * serial - 4 bytes, auto-incrementing
    * bigserial - 8 bytes, auto-incrementing
  * Money:
    * money - 8 bytes
  * Strings:
    * text - unlimited, varchar, preferred character storage in PostgreSQL
    * char(n) - fixed-length, blank-padded if < 'n' length, truncated if > 'n' length
    * char - equivalent to char(1), 1-character field
    * varchar(n) - variable length, no padding, with 'n' limit
    * varchar - variable length without any limits
  * Dates & Times (uses Julian Dates):
    * date - 32-bits, dates only
    * time - 64-bits, defaults to time without time zone, microsecond precision
    * time with time zone - 96-bits, date & time with time zone, microsecond precision
    * timestamp with time zone - 64-bits
    * timestamp without time zone - 64-bits
    * interval - 96-bits, range of time, microsecond precision
  * Boolean:
    * boolean - 8-bits, True (1)(on) or False (0)(off)
  * Network addresses:
    * cidr - 7 or 19-bytes, IPv4 or IPv6 networks
    * inet - 7 or 19-bytes, IPv4 or IPv6 hosts and networks
    * macaddr - 48-bits
  * XML
  * Arrays

### CREATE Objects

* Definition of objects is limited to 63 characters
* Identifiers MUST begin with alpha character
* Allows to create DBs, tables, schemas, indexes, functions, etc.
* The PostgreSQL hierarchy goes like:
  * DataBase
  * Schema(s) (optional, default schema is 'public')
  * Objects (tables, functions, triggers, etc.)
* All DBs have the `public` and `pg_catalog` schemas
* All users (and roles) have the CREATE and USAGE access to the `public` schema for ALL DBs

### DROP Objects

* Removes objects (DBs, schemas, tables, functions, triggers, etc.)
* Objects currently in use will NOT be dropped by default
* Dropping DBs will drop all the sub-objects, including schemas, tables, functions, etc.
* No active sessions must be ongoing while creating a database from a template
* Objects created with the `public` schema are not readily available without using the GRANT command
* `DROP <type> <name> CASCADE` will drop all the objects that are dependent on the `<name>` object

### ALTER Objects

* Allows changes to objects (DBs, schemas, tables, ecc.), especially name, structure, owner
* `ALTER DATABASE <old name> RENAME TO <new name>` will change ONLY the name (not the structure, not anything else) of `old name` into `new name`
* ALTER should be used without connections to the objects which is being altered
* `ALTER DATABASE <database name> OWNER TO <new owner>` will change the ownership of the `database name`DB to `new owner` (which can also be a role)
* `ALTER TABLE <old name> RENAME TO <new name>` will change ONLY the name of the table, leaving untouched the schema or the structure
* `ALTER TABLE <table name> ALTER COLUMN <column name> SET DATA TYPE <new type>` will change the structure of the table (may result in data loss if target column does NOT support target data)
* `ALTER TABLE <table name> ADD <column name> <column type>` will add a new column with a given data type
* Column names MUST be unique and cannot be added more than once
* `ALTER TABLE <table name> DROP COLUMN IF EXISTS <column name>` will remove the column (and all the existing data)&#x20;
* `ALTER ROLE <user name> SUPERUSER` will make the user as a superuser (with all privileges)
* `ALTER ROLE <old name> RENAME TO <new name>`  will change the name of the user (or the role), while clearing the MD5 hash of the password (which would then need to be reset) and updating all the ownerships of objects

### Constraints Usage

* Enforce storage requirements per table or per column
* Data types work as a basic constraint for value of columns
* `CREATE TABLE messages (date date NOT NULL)` will create the table messages where the date column must always be specified when adding a new row
* Multiple constraints can be bound to a single column
* Default column rule is to accept NULL values
* The `UNIQUE`constraint applies to any type of column, and requires that all the values in that column must be unique
* The creation of `UNIQUE` constraints cause PostgreSQL to automatically create a b-tree index on that column
* If we declare as `UNIQUE` a list of columns (like in `CREATE TABLE messages (date date, id bigint, message text, UNIQUE(id, message)`), the value contained in those columns can be repeated, but the combination of values cannot and must be unique
* `PRIMARY KEY` constraint is a combination of the `UNIQUE` and `NOT NULL` constraints
* There can only be ONE primary key per table
* Standard SQL recommends that each table contains a primary key
* The `FOREIGN KEY` constraint links tables with other tables
* `CREATE TABLE messagesCategories (id int REFERENCES messages(id), category text)` will create a foreign key constraint, so that the ids in the messagesCategories table must reflect values from the id column in the messages table
* &#x20;The `CHECK` constraint confirms column values based on some boolean criteria
* `CREATE TABLE messages (date date NOT NULL, id numeric CHECK(id > 0))` will store in the table only rows for which the id is positive
* `CREATE TABLE message (data date NOT NULL. id numeric CONSTRAINT positive_id CHECK(id > 0))` allows to specify a name for the constraint; if no name is specified, PostgreSQL will auto-generate a name for you

### INSERT Usage

* `INSERT` allows us to populate our tables
* `INSERT INTO <table name> VALUES ('2010-04-10', '1')` will require that the statement contains all the column values in order
* PostgreSQL inserts values left-to-right
* Rows that violate `CHECK` statement or other constraint will not be added
* `INSERT INTO <table name> (<column name>) VALUES(<value>)` will insert in a new row only the column which has been specified
* `INSERT INTO <table name> VALUES (<first row>), (<second row>), (<third row>)` will insert more than one record at a single time
* While inserting multiple rows, one error in one row will prevent ALL the records in the statement to be added, not only the one which generates the problem.
* Foreign key need not to be based on a numeric field

### COPY Usage

* It's a server-side command, different from the client-side `\copy` command
* Allows wholesale import/export of data from/to a file
* The file MUST be on the server and readable by the postgres user
* Uses absolute path for referencing the file
* Defaults to importing based on the TAB separator
* Does NOT work with views, but it will work with SELECTs of view
* The TRUNCATE command deletes all the records from a table, but leaves the table structure untouched
* `COPY <table name> FROM <file abs path> DELIMITER '<delimiter>'` will populate `<table name>` with the data contained in `<file abs path>`
* `COPY` will append the data at the end of the table
* `COPY <table name> TO <filename> DELIMITED '<delimiter>'` will export `<table name>` onto `<filename>` using `<delimiter>` as delimiter
* Exporting to an existing file will clear its contents and then write the new data.

### **SELECT Usage**

* Allows you to retrieve information or perform queries against your database
* `SELECT * FROM <table name>` will give us all the rows from table name
* `SELECT <column1 name, column2 name, ..., columnN name> FROM <table name>` will create a view of table name in which only information from `column1, column2, ..., columnN` are shown
* The order of the columns in the resulting view will reflect the order we gave in the `SELECT` command (e.g., `column1, column2, ..., columnN`), even if in the original table the order is different
* `SELECT <column1 AS col1, column2 AS col2, ...> FROM <table name>` will create aliases for column names in the resulting view
* `SELECT * FROM <table name> WHERE <expression>` will return a view in which only rows that satisfy the expression
* `SELECT * FROM <table name> WHERE <column name> LIKE '%str%'` will return all the rows in table name for which the value of column names have a successful string comparison with `str`
* `SELECT * FROM <table name> ORDER BY <column name> <ASC|DESC>` will order the results in the view by the values of column name in ascending (default) or descending order
* `SELECT DISTINCT <column name> FROM <table name>` will return a view in which only distinct (unique) values in column name are shown
* We should use the `ORDER BY` clause when using `LIMIT` because SQL does not guarantee to maintain the original sort order
* `SELECT * FROM <table name> ORDER BY <column name> ASC LIMIT <no of records>` will return a view in which only the first no or records rows are displayed
* `SELECT * FROM <table name> ORDER BY <column name> LIMIT <no of records> OFFSET <starting record>`will return a view with no of records rows after the first starting records

### JOIN Usage

* Aggregates related data across tables
* The default join is called the **CROSS JOIN**, i.e. the cartesian product of the two or more tables
* `SELECT * FROM <table1>, <table2>, ...` will give us the cross join of the tables
* `SELECT * FROM <table1> AS t1, <table2> AS t2 WHERE t1.id = t2.id` will perform an **INNER JOIN** binded on the id column of the two tables
* `SELECT * FROM <table1> AS t1, <table2> AS t2, <table3> AS t3 WHERE t1.id = t2.id AND t1.id = t3.id` will join three different tables based on the common id values
* `SELECT <col1>, <col2>, <col3> FROM <table1> AS t1, <table2> AS t2, <table3> AS t3 WHERE t1.id = t2.id AND t1.id = t3.id` will create a view in which only the selected columns are displayed
* `SELECT * FROM <table1> AS t1 INNER JOIN <table2> ON t1.id = <table2>.id` is functionally equivalent to a `JOIN` with a `WHERE` clause, but it's more elegant
* `SELECT * FROM <table1> AS t1 INNER JOIN <table2> USING (id)` is equivalent to the above query, but will not display the duplicate column
* **LEFT JOIN** will match all the values on the left table and include only values from the right table that match
* **RIGHT JOIN** will match all the values on the right table and include only values from the left table that match

### VIEW Usage

* Not a real object, it is a query that is executed upon invocation
* They may be based on 1 or more tables
* PostgreSQL supports temporary views (that last for session duration)
* Column names are auto-derived from the query
* `CREATE VIEW <name> AS SELECT * FROM <table1> INNER JOIN <table2> USING (<col>)` will create a permanent view of the inner join of these two tables
* When we will call `SELECT * FROM <name>`, PostgreSQL will execute the query on-the-fly and present us the result
* `SELECT id AS i, date AS d, category AS c FROM <name>` will alias the column names to i, d, and c
* `CREATE OR REPLACE VIEW <name> (<alias1>, <alias2>, <alias3>) AS SELECT * FROM <table1> INNER JOIN <table2> USING (<col>)` will create (or update) a permanent view of the inner join of these two tables
* `CREATE TEMP VIEW <name> (<aliases...>) AS SELECT <cols...> FROM <tables...>` will create a temporary view, that will be dropped when the session ends
* Temporary view are not assigned to the standard `public` schema, but rather to the `pg_temp_2` schema

### Aggregate Functions

* Compute single results (scalars) from multiple inputs (rows)
* Values are computed after the `WHERE` clause (they cannot used within the `WHERE` clause, but they can be used with the `HAVING` clause)
* The `HAVING` clause is calculated after the computation done by the aggregate function
* `SELECT count(*) FROM messages` will return the number of rows in the messages table
* `SELECT sum(id) FROM messages` will return the sum of value from the id column (only works with numbers, obviously)
* `SELECT avg(id) FROM messages` will return the average of the id values
* `SELECT min(id) FROM messages` will find the minimum value among the ids
* `SELECT max(id) FROM messages` will find the maximum value among the ids
* `SELECT date, min(id) FROM messages GROUP BY date` will not work without the `GROUP BY` clause, because we are referencing non-aggregated and aggregated columns in the same query
* `SELECT date, min(id) FROM messages WHERE id < 51 GROUP BY date` will execute the `WHERE` clause before the `GROUP BY` clause, so it will restrict the aggregate function to rows for which `id < 51`
* `SELECT date, min(id) FROM messages WHERE id < 51 GROUP BY date HAVING min(id) < 30` will perform the min function on the rows for which id is less than 50, group them by date, and the filter out the records for which `min(id)` is equal or greater than 30
* `SELECT bool_and(enabled) FROM messages` returns true if all the values are true (only works with booleans, obviously)
* `SELECT bool_or(enabled) FROM messages` will perform the boolean or operation on the values contained in the enabled column
* `SELECT string_agg(message, ' ') FROM messages;` will concatenate all the (string) values with single space delimiter

### UPDATE Usage

* Updates table(s) based on criteria
* Due to the PostgreSQL object-oriented philosophy, the `UPDATE` command will update tables and sub-tables (or parent tables) unless the `ONLY` keyword is specified
* The output will tell us how many records have been affected by the change
* `UPDATE messages SET enabled=f WHERE id = 100` will set the enabled value of the row with id 100 to false
* `UPDATE messages SET enabled=f, message = 'new message' WHERE id >= 100` will update multiple columns simultaneously
* `UPDATE messages SET enabled = DEFAULT` will reset all the rows (because the `WHERE` clause is missing) to the default value of the enabled column
* `UPDATE messages SET enabled = DEFAULT RETURNING *` will output all the rows that were affected by the modification (PostgreSQL-specific keyword)
* `UPDATE messages SET enabled = DEFAULT WHERE id >= 50 RETURNING enabled` will output the values of the enabled column that were affected by the change

### DELETE Records

* Removes entire records based on criteria
* Does NOT remove individual columns, only entire records
* Requires the table name, and preferable criteria (using the `WHERE` clause)
* It is a recursive operation (but you can use the ONLY clause to avoid affecting child tables)
* `DELETE FROM messages WHERE id = 103` removes a single record (if exists)
* `DELETE FROM messages WHERE id = 103 RETURNING *` will return all the records that have just deleted
* Just one `DELETE` statement could make the whole transaction fail, i.e. not deleting any rows if just one fails
* `DELETE FROM messages` deletes all rows from the messages table and all of its child tables
* `ALTER TABLE messagescategories DROP CONSTRAINT IF EXISTS messagescategories_id_fkey` will drop the foreign key constraint
* If you wish to delete all the records from a table, `TRUNCATE` is a better option with respect to `DELETE`

### INDEX Usage

* Speed up data retrieval and insertion
* Without indices, SQL performs sequential table scans looking for data
* It's convenient to create indices on columns that are frequently queried and/or joined
* During the creation of an index, only read operations are allowed (to maintain consistency)
* There is a max of 32 columns per index
* `EXPLAIN SELECT * FROM messages` will explain (and NOT execute) how the DB is going to execute the query (a query plan)
* `ALTER TABLE messages ADD primary key (id)` will create a B-tree (the data structure that implements the index of that column)
* `CREATE INDEX messages_id ON messages(messageid)` will create a new index on the table named `messages` based on the `messageid` column; if the index is formed by more than one column, their names must be divided by commas
* `EXPLAIN ANALYZE <query>` will give you an estimate of the query plan, and then actually execute it and give back the running time
* `\di messages` will display information about the indices on the messages table
* `DROP INDEX messages_id` will delete the whole index
* When you add a primary key constraint on a column, SQL will automatically create an index for you

### Built-in Functions

* Are mostly mathematical or string functions
* `SELECT f(number|column) FROM messages` will apply the function f on the number or the values from the columns selected
* Mathematical functions are:
  * `abs(n)` - absolute value
  * `sqrt(n)` or `cbrt(n)` - square or cubic root
  * `ceil(n)` or `floor(n)` - next highest or lowest integer
  * `div(n, m)` - divides n by m (return least integer)
  * `log(n)` - logarithm in base 10
  * `power(a, b)` - a to the power of b
  * `random()` - random value between 0 and 1
  * `round(n)` - rounds up or down to the nearest integer
  * `trunc(n, m)` - truncate n with m decimal values
  * `sin(n)` or `cos(n)` - classic trigonometric functions
* String functions are:
  * `bit_length(str)`
  * `char_length(str)`
  * `lower(str)`
  * `upper(str)`
  * `initcap(str)` - will capitalize the first letter of each word
  * `overlay('test' placing 'xx' from 2)` - will return txxt
  * `trim(both ' ' from ' test ')` - trims only leading and trailing, not between
  * `substring('test' from 2)` - will return st
  * `split_part(str, del, part)` - e.g. with `split_part('syslog message', ' ', 2)` we'll have `message`
* You can also have nested functions, e.g. `SELECT initcap(split_part(message, ' ', 2)) FROM messages LIMIT 10`

### Model Var Log

* A single line of the `/var/log/messages` file contains:
  * the timestamp (date and time)
  * the name of the host that submitted the message
  * the facility (the source of the message)
  * content of the message
* To create a database that we can use to model the content of a `/var/log/messages` file, we can:
  * create a `messages` table with the command `CREATE TABLE messages (mid BIGSERIAL PRIMARY KEY, mcatid smallint NOT NULL DEFAULT 1, mtime timestamp NOT NULL DEFAULT now(), mhost text NOT NULL DEFAULT 'Unknown Host', mfacility text NULL DEFAULT NULL, mmessage text NOT NULL DEFAULT 'NO MESSAGE')`
  * check that the table was created successfully with the command `\d messages`
  * test the insertion for a sample record with the command `INSERT INTO messages (mtime, mhost, mfacility, mmessage) VALUES (<date>, <hostname>, <facility_name>, <message>)`
  * write a script for reading all the lines in `/var/log/messages`, and adapt them into a format suitable for bulk-loading into PostgreSQL
  * bulk-load the records from the file with the command `COPY messages (mtime, mhost, mfacility, mmessage) FROM <path< DELIMITER ';'`
  * create a category for joins with the command `INSERT INTO messagecategories VALUES ('1', 'NOTICE')`
  * perform a join with the messagecategories table with the command `SELECT mtime, mhost, mfacility, messagecategories.name, mmessage FROM messages as m INNER JOIN messagecategories ON messagecategories.cid = m.catid`

### GRANT Usage

* The `GRANT` command is available to grant privileges of all sorts:
  * `SELECT` - columns or tables
  * `INSERT` - columns or tables
  * `UPDATE`
  * `DELETE` - row-based
  * `CREATE`
  * `CONNECT` (granted by default)
  * `EXECUTE` - functions
  * `TRIGGER`
  * `USAGE`
  * `TEMPORARY`
  * `TRUNCATE`
  * `REFERENCES`
* The objects are owned by creators (owners and/or superusers), non-superuser do not have access to them (unless they are granted the privileges)
* We can use the `\dp <obj_name>` to reveal privileges related to that object
* UPDATE and DELETE privileges require SELECT privileges for criteria application
* `GRANT SELECT (<columns>) ON <obj_name> TO <role>` will grant the `SELECT` privileges just on those columns. For setting the privileges to all columns, it is sufficient `GRANT SELECT ON <obj_name>`
* `GRANT INSERT ON <obj_name> TO <role>` will grant the `INSERT` privileges on all columns of obj\_name
* `GRANT USAGE ON <obj_name> TO <role>` will grant the `USAGE` privileges (useful for sequences of auto-generated values)
* `UPDATE messages SET mfacility = 'kernel' WHERE mid = '204358' RETURNING *` will update the row for which the `WHERE` clause applies, and will display the whole row (this command is specific to PostgreSQL)
* `GRANT ALL ON <obj_name> TO <role>` will grant all privileges to role

### REVOKE Privileges

* `REVOKE` is the converse of `GRANT`, and it's used to un-assign privileges
* The privileges are represented by a single letter, and have the following meanings:
  * a: `INSERT` (append)
  * r: `SELECT` (read)
  * w: `UPDATE` (write)
  * d: `DELETE`
  * D: `DROP` (truncate)
  * x: references
  * t: triggers
  * U: usage
  * /user: permission issuer
* `REVOKE ALL on object[, object2, ..., objectN] FROM user` removes ALL privileges for user on `object` (and `object2, ..., objectN`)
* `REVOKE INSERT on object[, object2, ..., objectN] FROM user` removes just the `INSERT` privilege for user on `object` (and `object2, ..., objectN`). To revoke more than one privilege, the command is `REVOKE privilege[, privilege2, ..., privilegeN] on object[, object2, ..., objectN] FROM user`
* The `INSERT` privilege may be granted/revoked independently from `SELECT`, unlike the `UPDATE` and `DELETE` ones (where `SELECT` is used to match the criteria specified with the `WHERE` clause).
* `GRANT ALL on object TO user WITH GRANT OPTION` will assign all the privileges to user on object, and also will enable him/her to hand over (`GRANT`) these privileges to other users. This is represented by a `*` after each of the privileges he/she has been granted.
* If we try to `REVOKE` some privileges to a user which has `GRANTED` the same privileges to some other user, the `REVOKE` operation will fail, unless we specify the `CASCADE` option.

### DB Backup

* It's mainly achieved by using `pg_dump` (for individual tables or DBs) or `pg_dumpall` (for the full DBMS)
* Both commands operate on running DBs
* The possible outputs of these commands are:
  * an SQL script, designed for full replay, to be used with the psql utility
  * an archive (only for `pg_dump`), designed to allow selective and/or reordered restores, to be used with the `pg_restore` utility
    * `-Fp` (default): plain SQL script, uncompressed
    * `-Fc`: auto-compressed format, works only with `pg_restore`
    * `-Ft`: tarball, not compressed, works with both `tar` and `pg_restore`
* `pg_dump postgres` - dumps the postgres DB to STDOUT (if you are able to authenticate)
* `pg_dump -v -f DB_Backup_postgres postgres` - dumps the postgres DB to the `DB_Backup_postgres` file, in verbose mode
* `pg_dump -v postgres > DB_Backup_postgres2` - dumps the postgres DB to the `DB_Backup_postgres2` file, using output redirection
* `pg_dump -s -f DB_Backup.schema postgres` - dumps only the schema of the postgres database to the `DB_Backup.schema` file
* `pg_dump -t messages -t categories -f DB_Backup.messages.categories postgres` - dumps the messages and categories tables of the postgres database to the `DB_Backup.messages.categories` file
* `pg_dump -t 'messages*' -f DB_Backup.ALL_messages postgres` - dumps all the tables of the postgres whose name starts with messages  database to the `DB_Backup.ALL_messages`  file
* `pg_dump -Fc -f DB_Backup.compressed postgres` - dumps the postgres database in the compressed format into the `DB_Backup.compressed` file
* `pg_dump -Ft -f DB_Backup.postgres.tar postgres` - dumps the postgres database in the tar format into the `DB_Backup.postgres.tar` file
* `pg_dumpall -f DB_Backup.ALL` - dumps the whole DBMS into the `DB_Backup.ALL` file
* You can avoid to authenticate each time a table is being copied by creating a file called `.pgpass` in the home directory

### DB Restore

* There are two tools to perform this operation: `psql` and `pg_restore`
* `pg_restore -v -d postgres DB_Backup.postgres.compressed` will perform a full restoration of the postgres DB from the `DB_Backup.postgres.compressed` file (in verbose mode)
* `pg_restore -v -d postgres DB_Backup.postgres.tar` will perform a full restoration of the postgres DB from the `DB_Backup.postgres.tar` file (in verbose mode)
* The database referenced with the `-d` option must be already existing; you can create it anew with the `-C` option
* `pg_restore -l backup_file` will display the TOC (table of contents) of the backup, that you can use for selective/reordered restoration
* `psql -f DB_Backup.linuxcbt2.sql` will restore the database whose definition is contained in `DB_Backup.linuxcbt2.sql` (only for files encoded as scripts)
* `pg_restore -v -d postgres -t messages2 DB_Backup.postgres.compressed` will restore just the `messages2` table in the postgres database from the `DB_Backup.postgres.compressed` file
* `pg_restore -v -d postgres -t messagescategories DB_Backup.postgres.tar` will restore just the `messagescategories` table in the postgres database from the `DB_Backup.postgres.tar` file

### Windows Restore

* `psql -h 192.168.75.105` will connect to the PostgreSQL databse running on host 192.168.75.105
* `psql -h 192.168.75.105 -f DB_Backup.ALL` will run the DB\_Backup.ALL script against the databse running on host 192.168.75.105
* `pg_restore -h 192.168.75.105 -v -d postgres -t messages2 DB_Backup.postgres.compressed` will restore the messages2 table on the postgres database running on host 192.168.75.105, taking the values from the `DB_Backup.postgres.compressed` file
* The HBA conf file is the one that allows Postgres to accept incoming remote connections

### SSH Tunnels

* By default, PostgreSQL uses plaintext communication between the client and the server
* `tcpdump -v -i lo tcp port 5432 -w postgres.dump.1` allows us to sniff the communication PostgreSQL is receiving and sending in the localhost, and save it to the file named `postgres.dump.1`
* We can analyze the dump file using Wireshark to reconstruct the whole communication stream
* `ssh -L 5433:192.168.75.20 192.168.75.20` will create a secure tunnel between localhost and 192.168.75.20 (the communication in transit will be encrypted, but not on the endpoints)
* `netstat -ntl | grep 5433` will confirm that the tunnel is actually existing
* `psql -h localhost -p 5433` will connect to the 5433 port on the loopback interface, which is acting as the tunnel to 192.168.75.20
* You can connect via SSH to a remote instance of Postgres using PuTTY (or some equivalent SSH client), but Windows cannot act as the SSH server unless you use Cygwin or similar

### SSL Connections

* Enables true end-to-end encryption protection
* Postgres will listen for SSL clients on the usual port, 5432
* Postgres will auto-negotiate the connection with the client, unless explicitly stated otherwise in the `pg_hba.conf` file
* `openssl req -new -text -out server.req` will generate a request to be signed
* `openssl rsa -in privkey.pem -out server.key` will remove the passphrase from the generated private key
* `openssl req -x509 -in server.req -text -key server.key -out server.crt` will generate a self-signed certificate file
* `chown postgres server.key && chmod 600 server.key` will change the owner of the private key to postgres, and make it readable just to that user
* Changing the `postgresql.conf` file to have the `ssl=on` key will enable SSL (out by default)
* When you want to use SSL, you must specify the host (even if it is localhost) with the `-h` option. If you connect locally without the `-h` option, it will use Unix Domain Sockets, which do not support SSL

## Resources

### Articles

* [10 Postgres tips for beginners](https://postgres.ai/blog/20230722-10-postgres-tips-for-beginners) - Nikolay Samokhvalov, Postgres.ai
* [psql Tips](https://mydbanotebook.org/psql\_tips\_all.html) - Lætitia Avrot
* [PostgreSQL DBA Daily Checklist](https://minervadb.xyz/postgresql-dba-daily-checklist/) - Shiv Iyer, MinervaDB

### Books

* [The Internals of PostgreSQL](https://www.interdb.jp/pg/index.html) - Hironobu Suzuki

### Websites

* [Crunchy Data](https://www.crunchydata.com/developers/tutorials) - Postgres Tutorials
* [explain.dalibo.com](https://explain.dalibo.com/) - PostgreSQL execution plan visualizer


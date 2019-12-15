# Manual of mssql-restore

This script is designed to simplify the import of backup files for MS SQL Server 2017 databases (other versions of MS SQL Server have not been tested).
To interact with the DBMS inside this script, an appeal to the console tool sqlcmdfrom the MS SQL Server 2017 for Linux distribution is used.
This script allows you to restore backup copies of arbitrary databases of the ".bak" format to arbitrary instances of the MS SQL Server DBMS in automatic mode.
Simplification of the import procedure is achieved by automating the preparation of specific import procedures comparing all the data tables present in the backup file with their actual location in the form of files in the DBMS storage. As well as by automating the implementation of the common operations preparing the database for importing data into it, and automating the operations executing after by the import. This script also allows you to create users for the imported database (launch attribute `:--auth`). In order for the user to be created in the imported database, a password must be specified for him. For the user created in the database, the rights to read and write to this database will be automatically assigned. If a user is specified in the `--auth` attribute, but no password is specified for it (attribute value format:
```--auth loginUser1: passwordUser1, loginUser2, loginUser3: passwordUser3``` - the password for `loginUser2` is not specified in this entry), then this user will not be created in the imported database, but if this user already exists in the imported database, then the rights for it will be automatically matched / restored after import by the method autocorrection of orphaned users.

If a user for whom a password is not specified is not present in the database at all, then no actions will be performed for him.
This script allows you to specify in the import attribute (`--bakpath`) both the backup file itself and the directory in which these files are located. If you specify a directory, all backup files from it will be imported.
If you specify a backup file as the launch attribute, it is possible to specify the name of the database (launch attribute: `--dbname`) into which you want to execute of import. If the directory where the backup files are located is specified as an attribute, the names of the databases into which the backup files will be imported will be taken from the names of the backup files by dropping their extension.
When you run this script in a terminal session open in an SSH connection, during long-term import, the connection will be automatically supported - for this, an animated preloader is used, the purpose of which is to periodically send data to the SSH client to maintain the session. When directing the output of this script to a non-terminal session, the preloader will not be involved.

Options:
 * `--auth`         This parameter in the format: "loginUser1:passwordUser1,loginUser2,loginUser3:passwordUser3" specifies the data for granting user rights to the imported database (the parameter is not used by default).

* `--bakpath`      Path to find backups - a file or directory is specified (by default, the parameter uses the location directory of this utility).

* `--config`       Use configuration file (the parameter is not used by default).

* `--dbname`       Forcibly set the name for the restoring database (by default, the value corresponds to the name of the backup file without the extension).

* `--dbpath`       Specify the database storage directory (the default value is "/var/opt/mssql/data").

* `--help`         Show this help.

* `--host`         The host for connecting to the DBMS (the default value is "localhost").

* `--port`         Port for connecting to the DBMS (by default, the parameter is set to "1433").

* `--sapass`       Super administrator password (by default, taken from the global variable SA_PASSWORD).

* `--save_config`  Save an example config with all possible parameters.

* `--version`      Print the version of this program.

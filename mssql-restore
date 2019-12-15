#!/bin/bash
DBMS_HOST=localhost
DBMS_PORT=1433
TARGET_DIR="`readlink -f "$(dirname "$0")"`"
VERSION=0.2
LANG_CLI="`locale 2> /dev/null | grep 'ru_RU'`"
[ -z "$DB_PATH" ] && DB_PATH=/var/opt/mssql/data
PATH=$PATH:/opt/mssql-tools/bin

if [ -n "$LANG_CLI" ]
then
	MESS__BACKUPS_DO_NOT_EXIST='Резервных копий нет :('
	MESS__NOT_CONNECT_TO_DB='Не могу подключиться к СУБД'
	MESS__START_OF_IMPORT_OF_REPLICATION='Старт импорта данных репликации для БД:'
	MESS__IMPORT_COMPLETED='Импорт данных завершен для БД:'
	MESS__CONFLICT_OF_ARGUMENTS_AND_PARAMETERS="Конфликт параметров! Атрибут \"$1\" задает тот же параметр что и значение \"$2\" в указанном конфигурационном файле."
	MESS__CONFIGURATION_FILE_IS_INCORRECTLY_SPECIFIED='Файл конфигурации указан неверно.'
	MESS__SQLCMD_NOT_FOUND='Ошибка! Команда "sqlcmd" не найдена!'
	MESS__SA_PASSWORD_MUST_BE_SPECIFIED='Пароль суперадминистратора должен быть указан.'
	MESS__EITHER_THE_FILE_OR_THE_DIRECTORY='Можно указать либо файл резервной копии, либо каталог, в котором файлы резервной копии находятся.'
	MESS__SPECIFY_NAME_FOR_THE_RESTORING_DB_ONLY_WITH_THE_ATTRIBUTE_POINTING_TO_BACKUP_FILE='Указать имя для восстанавливаемой базы данных можно только с атрибутом указывающим на файл бекапа.'
	MESS__ERROR_CREATING_DATABASE_DESTINATION_DIRECTORY='Ошибка создания директории назначения БД:'
	MESS__CONFIGURATION_FILE_WAS_SAVED='Пример конфига сохранен в файл:'
	MESS__HELP='Параметры:
 --auth         В этом параметре в формате: "loginUser1:passwordUser1,loginUser2,loginUser3:passwordUser3" указываются данные для предоставления прав пользователю на импортируемую БД  (по умолчанию параметр не используется).
 --bakpath      Путь нахождения резервных копий - указывается файл или директория (по умолчанию параметр использует директорию нахождения данной утилиты).
 --config       Использовать файл конфигурации (по умолчанию параметр не используется).
 --dbname       Задать принудительно имя для восстанавливаемой БД (по умолчанию значение соответствует имени файла бэкапа без расширения).
 --dbpath       Указать директорию хранения БД (по умолчанию имеет значение "/var/opt/mssql/data").
 --help         Показать эту справку.
 --host         Хост подключения к СУБД (по умолчанию параметр имеет значение "localhost").
 --port         Порт подключения к СУБД (по умолчанию параметр имеет значение "1433").
 --sapass       Пароль суперадминистратора (по умолчанию берется из глобальной переменной SA_PASSWORD).
 --save_config  Сохранить пример конфигурационного файла со всеми возможными параметрами.
 --version      Вывести версию данной программы.'
	CONFIG_CONTENT="### Файл конфигурации для Database Recovery Utility for MS SQL Server версии: $VERSION

# Пароль суперадминистратора:
# SA_PASSWORD=

# Пользователи и их пароли (опционально) для предоставления прав на импортируемую базу данных (формат: \"loginUser1:passwordUser1,loginUser2,loginUser3:passwordUser3\"):
# SQL_USERS_AND_THEIR_PASSWORDS_FOR_DB=

# Хост подключения к СУБД:
# DBMS_HOST=

# Порт подключения к СУБД:
# DBMS_PORT=

# Путь нахождения резервных копий - указывается файл или директория:
# BACKUP_PATH=

# Задать принудительно имя для восстанавливаемой БД:
# DB_NAME=

# Указать директорию хранения БД:
# DB_PATH="
else
	MESS__BACKUPS_DO_NOT_EXIST='Backups do not exist :('
	MESS__NOT_CONNECT_TO_DB='i can not connect to DBMS'
	MESS__START_OF_IMPORT_OF_REPLICATION='Start of import of replication data for the DB:'
	MESS__IMPORT_COMPLETED='Data import completed for DB:'
	MESS__CONFLICT_OF_ARGUMENTS_AND_PARAMETERS="Conflict of parameters! The attribute \"$1\" sets the same parameter as the value \"$2\" in the specified configuration file."
	MESS__CONFIGURATION_FILE_IS_INCORRECTLY_SPECIFIED='The configuration file is incorrectly specified.'
	MESS__SQLCMD_NOT_FOUND='Error! The command "sqlcmd" was not found!'
	MESS__SA_PASSWORD_MUST_BE_SPECIFIED='The super administrator password must be specified.'
	MESS__EITHER_THE_FILE_OR_THE_DIRECTORY='Either the backup file or the directory in which the backup files are located can be specified.'
	MESS__SPECIFY_NAME_FOR_THE_RESTORING_DB_ONLY_WITH_THE_ATTRIBUTE_POINTING_TO_BACKUP_FILE='You can specify a name for the restoring database only with the attribute pointing to the backup file.'
	MESS__ERROR_CREATING_DATABASE_DESTINATION_DIRECTORY='Error creating the database destination directory:'
	MESS__CONFIGURATION_FILE_WAS_SAVED='An example config was saved to the file:'
	MESS__HELP='Options:
 --auth         This parameter in the format: "loginUser1:passwordUser1,loginUser2,loginUser3:passwordUser3" specifies the data for granting user rights to the imported database (the parameter is not used by default).
 --bakpath      Path to find backups - a file or directory is specified (by default, the parameter uses the location directory of this utility).
 --config       Use configuration file (the parameter is not used by default).
 --dbname       Forcibly set the name for the restoring database (by default, the value corresponds to the name of the backup file without the extension).
 --dbpath       Specify the database storage directory (the default value is "/var/opt/mssql/data").
 --help         Show this help.
 --host         The host for connecting to the DBMS (the default value is "localhost").
 --port         Port for connecting to the DBMS (by default, the parameter is set to "1433").
 --sapass       Super administrator password (by default, taken from the global variable SA_PASSWORD).
 --save_config  Save an example config with all possible parameters.
 --version      Print the version of this program.'
	CONFIG_CONTENT="### Configuration File for Database Recovery Utility for MS SQL Server. Version $VERSION

# Super administrator password.
# SA_PASSWORD=

# Users and their passwords (optional) for granting rights to the imported database (format: \"loginUser1:passwordUser1,loginUser2,loginUser3:passwordUser3\"):
# SQL_USERS_AND_THEIR_PASSWORDS_FOR_DB=

# The host for connecting to the DBMS:
# DBMS_HOST=

# Port for connecting to the DBMS:
# DBMS_PORT=

# Path to find backups - a file or directory is specified:
# BACKUP_PATH=

# Forcibly set the name for the restoring database:
# DB_NAME=

# Specify the database storage directory:
# DB_PATH="
fi

save_config()
{
	if [ -z "${ARGUMENTS_ARRAY[$((INC+1))]}" ]
	then
		SAVING_CONFIG_FILE="$TARGET_DIR/ms_sql_restore.conf"
	elif [ -z "`echo ${ARGUMENTS_ARRAY[$((INC+1))]} | grep '\.conf$'`" ]
	then
		SAVING_CONFIG_FILE="${ARGUMENTS_ARRAY[$((INC+1))]}.conf"
	else
		SAVING_CONFIG_FILE="${ARGUMENTS_ARRAY[$((INC+1))]}"
	fi

	echo "$CONFIG_CONTENT" > "$SAVING_CONFIG_FILE" && echo $MESS__CONFIGURATION_FILE_WAS_SAVED $SAVING_CONFIG_FILE
	exit 0
}

INC=0
ARGUMENTS_ARRAY=("$@")

for INT in "${ARGUMENTS_ARRAY[@]}"
do
	case "$INT"
	in
		'--auth'		) SQL_USERS_AND_THEIR_PASSWORDS_FOR_DB=${ARGUMENTS_ARRAY[$((INC+1))]} ;;
		'--bakpath'		) BACKUP_PATH=${ARGUMENTS_ARRAY[$((INC+1))]} ;;
		'--config'		) CONFIG_FILE=${ARGUMENTS_ARRAY[$((INC+1))]} ;;
		'--dbname'		) DB_NAME=${ARGUMENTS_ARRAY[$((INC+1))]} ;;
		'--dbpath'		) DB_PATH=${ARGUMENTS_ARRAY[$((INC+1))]} ;;
		'--help'		) { echo -e "$MESS__HELP"; exit; } ;;
		'--host'		) DBMS_HOST=${ARGUMENTS_ARRAY[$((INC+1))]} ;;
		'--port'		) DBMS_PORT=${ARGUMENTS_ARRAY[$((INC+1))]} ;;
		'--sapass'		) SA_PASSWORD=${ARGUMENTS_ARRAY[$((INC+1))]} ;;
		'--save_config'	) save_config ;;
		'--version'		) { echo "Database Recovery Utility for MS SQL Server. Version $VERSION"; exit; } ;;
	esac
	(( INC++ ))
done
unset INC

if ! which sqlcmd &> /dev/null
then
	echo $MESS__SQLCMD_NOT_FOUND
	exit 1
fi

if [ -n "$CONFIG_FILE" ]
then
	LIST_ARGUMENTS="`for INT in "${ARGUMENTS_ARRAY[@]}"; do echo "$INT"; done`"

	conflict_of_arguments_and_parameters()
	{
		echo $MESS__CONFLICT_OF_ARGUMENTS_AND_PARAMETERS
		exit 1
	}

	if [ "`file -b $CONFIG_FILE`" == 'ASCII text' ]
	then
		[ -n "`echo "$LIST_ARGUMENTS" | grep -x '\-\-auth'`" -a -n "`grep '^SQL_USERS_AND_THEIR_PASSWORDS_FOR_DB=' "$CONFIG_FILE"`" ] && conflict_of_arguments_and_parameters --auth SQL_USERS_AND_THEIR_PASSWORDS_FOR_DB
		[ -n "`echo "$LIST_ARGUMENTS" | grep -x '\-\-bakpath'`" -a -n "`grep '^BACKUP_PATH=' "$CONFIG_FILE"`" ] && conflict_of_arguments_and_parameters --bakpath BACKUP_PATH
		[ -n "`echo "$LIST_ARGUMENTS" | grep -x '\-\-dbname'`" -a -n "`grep '^DB_NAME=' "$CONFIG_FILE"`" ] && conflict_of_arguments_and_parameters --dbname DB_NAME
		[ -n "`echo "$LIST_ARGUMENTS" | grep -x '\-\-dbpath'`" -a -n "`grep '^DB_PATH=' "$CONFIG_FILE"`" ] && conflict_of_arguments_and_parameters --dbpath DB_PATH
		[ -n "`echo "$LIST_ARGUMENTS" | grep -x '\-\-host'`" -a -n "`grep '^DBMS_HOST=' "$CONFIG_FILE"`" ] && conflict_of_arguments_and_parameters --host DBMS_HOST
		[ -n "`echo "$LIST_ARGUMENTS" | grep -x '\-\-port'`" -a -n "`grep '^DBMS_PORT=' "$CONFIG_FILE"`" ] && conflict_of_arguments_and_parameters --port DBMS_PORT
		[ -n "`echo "$LIST_ARGUMENTS" | grep -x '\-\-sapass'`" -a -n "`grep '^SA_PASSWORD=' "$CONFIG_FILE"`" ] && conflict_of_arguments_and_parameters --sapass SA_PASSWORD

		source $CONFIG_FILE
	else
		echo $MESS__CONFIGURATION_FILE_IS_INCORRECTLY_SPECIFIED
		exit 1
	fi
fi

[ -z "$BACKUP_PATH" ] && BACKUP_PATH=$TARGET_DIR

if [ -z "$SA_PASSWORD" ]
then
	echo $MESS__SA_PASSWORD_MUST_BE_SPECIFIED
	exit 1
elif [ -z "`file -b "$BACKUP_PATH" 2> /dev/null | grep -x directory`" ]
then
	if [ -z "$(file -b "$BACKUP_PATH" 2> /dev/null | grep -x data)" ]
	then
		echo $MESS__SPECIFIED_ARGUMENT_IS_NOT_A_BACKUP_FILE
		exit 1
	fi

	TARGET_DIR="$(dirname "`realpath "$BACKUP_PATH"`")"
	BACKUPS_FILES_ARRAY[1]="`basename "$BACKUP_PATH"`"
	[ -z "$DB_NAME" ] && RELATED_DB_ARRAY[1]="`echo "${BACKUPS_FILES_ARRAY[1]}" | sed 's/.bak//g'`" || RELATED_DB_ARRAY[1]=$DB_NAME
elif [ -n "$DB_NAME" ]
then
	echo $MESS__SPECIFY_NAME_FOR_THE_RESTORING_DB_ONLY_WITH_THE_ATTRIBUTE_POINTING_TO_BACKUP_FILE
	exit 1
fi

while read USER_FOR_DB PASSWORD_OF_USER_FOR_DB
do
	(( INC++ ))
	USER_FOR_DB_ARRAY[$INC]=$USER_FOR_DB
	PASSWORD_OF_USER_FOR_DB_ARRAY[$INC]=$PASSWORD_OF_USER_FOR_DB
done <<< "`echo "$SQL_USERS_AND_THEIR_PASSWORDS_FOR_DB" | sed 's/:/ /g; s/,/\n/g'`"
unset INC

EXIST_DB_LIST="`sqlcmd -S $DBMS_HOST,$DBMS_PORT -U SA -P "$SA_PASSWORD" -Q "select * from sys.databases" | awk '{print $1}' | tail -n +3 | grep -v ^\( | grep -v "^$"`"

if [ -z "`echo "$EXIST_DB_LIST" | grep -x 'master'`" ]
then
	echo $MESS__NOT_CONNECT_TO_DB
	exit 1
fi

if [ -n "$DB_PATH" ]
then
	mkdir -p "$DB_PATH"
	DB_PATH="`realpath "$DB_PATH"`"

	if [ -z "`file -b "$DB_PATH" 2> /dev/null | grep -x directory`" ]
	then
		echo $MESS__ERROR_CREATING_DATABASE_DESTINATION_DIRECTORY $DB_PATH
		exit 1
	fi

	chmod 700 "$DB_PATH"
fi

if [ -z "${BACKUPS_FILES_ARRAY[1]}" ]
then
	TARGET_DIR="`realpath "$BACKUP_PATH"`"

	while read DISCOVERED_BACKUP
	do
		if [ -n "$(file -b "$TARGET_DIR/$DISCOVERED_BACKUP" 2> /dev/null | grep -x data)" ]
		then
			(( INC++ ))
			BACKUPS_FILES_ARRAY[$INC]=$DISCOVERED_BACKUP
			RELATED_DB_ARRAY[$INC]="`echo $DISCOVERED_BACKUP | sed 's/.bak//g'`"
		fi
	done <<< "`( cd "$TARGET_DIR" && ls *.bak ) 2> /dev/null`"
	
	if [ -z "${BACKUPS_FILES_ARRAY[1]}" ]
	then
		echo "$MESS__BACKUPS_DO_NOT_EXIST"
		exit 0
	fi
fi

animation_executing()
{
	background_process()
	{
		echo -n " "
		SHAG="1"
		KADR='-\|/'
		while true
		do
			printf "\b${KADR:SHAG++%${#KADR}:1}"
			sleep 2
		done
	}
	background_process "${1}" &
	PID_OF_PRELOADER="$!"
}

for INT in $(seq 1 $INC)
do
	DATA_AND_FILE_MATCHING_STRING="`sqlcmd -S $DBMS_HOST,$DBMS_PORT -U SA -P "$SA_PASSWORD" -Q "RESTORE FILELISTONLY FROM DISK = N'$TARGET_DIR/${BACKUPS_FILES_ARRAY[$INT]}'" \
	| sed 's/ //g; s/[A-Z]\:\\\\/ /g; s/\.mdf/\.mdf\n/g; s/\.ndf/\.ndf\n/g; s/\.ldf/\.ldf\n/g; s/\\\\/\\\\ /g' | grep df$`"

	MDF_NAME="`echo "$DATA_AND_FILE_MATCHING_STRING" | grep \.mdf$ | awk '{print $1}'`"
	LDF_NAME="`echo "$DATA_AND_FILE_MATCHING_STRING" | grep \.ldf$ | awk '{print $1}'`"

	while read NDF_NAME
	do
		(( INC++ ))
		NDF_NAME_ARRAY[$INC]=$NDF_NAME
		NDF_FILE_ARRAY[$INC]="${RELATED_DB_ARRAY[$INT]}_$INC.ndf"
	done <<< "`echo "$DATA_AND_FILE_MATCHING_STRING" | grep \.ndf$ | awk '{print $1}'`"
	unset INC

	[ -z "`echo "$EXIST_DB_LIST" | grep -x "${RELATED_DB_ARRAY[$INT]}"`" ] && sqlcmd -S $DBMS_HOST,$DBMS_PORT -U SA -P "$SA_PASSWORD" -Q "CREATE DATABASE ${RELATED_DB_ARRAY[$INT]} `\
	[ -n "$MDF_NAME" ] && echo "ON ( NAME = $MDF_NAME, FILENAME = '$DB_PATH/${RELATED_DB_ARRAY[$INT]}.mdf' ) "``\
	[ -n "$LDF_NAME" ] && echo "LOG ON ( NAME = $LDF_NAME, FILENAME = '$DB_PATH/${RELATED_DB_ARRAY[$INT]}_log.ldf' )"`"

	echo -n `date +%F\ %T` $MESS__START_OF_IMPORT_OF_REPLICATION \"${RELATED_DB_ARRAY[$INT]}\"
	[ -t 1 ] && animation_executing

	sqlcmd -S $DBMS_HOST,$DBMS_PORT -U SA -P "$SA_PASSWORD" -Q "USE MASTER; ALTER DATABASE [${RELATED_DB_ARRAY[$INT]}] SET SINGLE_USER WITH ROLLBACK IMMEDIATE" 1> /dev/null

	sqlcmd -S $DBMS_HOST,$DBMS_PORT -U SA -P "$SA_PASSWORD" -Q "RESTORE DATABASE [${RELATED_DB_ARRAY[$INT]}] FROM DISK = N'$TARGET_DIR/${BACKUPS_FILES_ARRAY[$INT]}' WITH `\
	[ -n "$MDF_NAME" ] && echo "MOVE '$MDF_NAME' TO '$DB_PATH/${RELATED_DB_ARRAY[$INT]}.mdf', "``\
	[ -n "${NDF_NAME_ARRAY[1]}" ] && for i in "${NDF_NAME_ARRAY[@]}"; do (( INC++ )); echo "MOVE '${NDF_NAME_ARRAY[$INC]}' TO '$DB_PATH/${NDF_FILE_ARRAY[$INC]}', "; done``\
	[ -n "$LDF_NAME" ] && echo "MOVE '$LDF_NAME' TO '$DB_PATH/${RELATED_DB_ARRAY[$INT]}_log.ldf',"` REPLACE, STATS = 10"
	unset MDF_NAME LDF_NAME NDF_NAME_ARRAY[@] NDF_FILE_ARRAY[@]

	sqlcmd -S $DBMS_HOST,$DBMS_PORT -U SA -P "$SA_PASSWORD" -Q "USE MASTER; ALTER DATABASE [${RELATED_DB_ARRAY[$INT]}] SET MULTI_USER"

	for USER_FOR_DB in "${USER_FOR_DB_ARRAY[@]}"
	do
		(( INC++ ))
		[ -n "${PASSWORD_OF_USER_FOR_DB_ARRAY[$INC]}" ] && sqlcmd -S $DBMS_HOST,$DBMS_PORT -U SA -P "$SA_PASSWORD" -Q "USE ${RELATED_DB_ARRAY[$INT]}; create user $USER_FOR_DB from login $USER_FOR_DB; ALTER LOGIN $USER_FOR_DB WITH PASSWORD = '${PASSWORD_OF_USER_FOR_DB_ARRAY[$INC]}'; EXEC sp_addrolemember 'db_datareader', $USER_FOR_DB; EXEC sp_addrolemember 'db_datawriter', $USER_FOR_DB; GRANT EXECUTE TO $USER_FOR_DB;" &> /dev/null
		sqlcmd -S $DBMS_HOST,$DBMS_PORT -U SA -P "$SA_PASSWORD" -Q "USE ${RELATED_DB_ARRAY[$INT]}; EXEC sp_change_users_login 'Auto_Fix', '$USER_FOR_DB'" &> /dev/null
	done
	unset INC

	if [ -t 1 ]
	then
		{
			kill $PID_OF_PRELOADER
			printf "\b"
			echo ""
			tput cuu 1
			tput el
		} 2> /dev/null
	fi

	echo `date +%F\ %T` $MESS__IMPORT_COMPLETED \"${RELATED_DB_ARRAY[$INT]}\"
done

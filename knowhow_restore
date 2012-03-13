#!/bin/bash


VER=2.0.0

AUTHOR="hernad@bring.out.ba" 
DAT=05.03.2012

echo $DAT, $VER, $AUTHOR

USER=`whoami`
P_PORT=5432


usage_1() {

 echo "  usage: $0 <hostname> <username> <dbname>  <filename (bez dump.gz)>"
 echo "example: $0 localhost admin bringout bring_2012-14-01"
 echo "         na lokaciji ~/.f18/backup" 
 echo "         treba da se arhiva bring_2012-14-01.dump.gz"

 exit 1

}


if [[ "$USER" == "postgres" &&  "$4" == "" ]]; then

echo ubuntu/postgres varijanta .... 

    HOST_NAME=
    DB_NAME=$1
    BACKUP_NAME="$2"

    METRIC_CNT=`echo 'select count(*) from metric' | psql $DB_NAME 2>/dev/null | grep -c '1 row'`

else

    HOST_NAME="$1"
    P_USER="$2"
    DB_NAME="$3"
    BACKUP_NAME="$4"


    if [[ "$1" == "" || "$2" == "" || "$3" == "" || "$4" == "" ]]
    then
        usage_1	 
    fi

    METRIC_CNT=0
fi

B_DIR=~/.f18/backup

DUMP_FILE=$BACKUP_NAME.dump
GZIP_FILE=$DUMP_FILE.gz

if ! [[ -d "$B_DIR" ]]
then
	echo "kreiram backup directory: $B_DIR"
	mkdir -p "$B_DIR" 
else
    ls -l -h "$B_DIR"/*
fi


if [[ "$METRIC_CNT" == "1" ]]
then
   echo " "
   echo "!!!!!!!!!!!!!!!!!!!!!!!!!!!!!"
   echo " "
   echo "$DB_NAME nije prazna ! Restore se mora raditi na praznoj bazi !"
   echo "kraj"
   exit 1
fi



echo ""
if ! [[ -f  "$B_DIR/$GZIP_FILE" ]]
then
 
  echo "nema gzip $B_DIR/$GZIP_FILE !?"
  echo " "
  exit 1
fi


cp -av "$B_DIR/$GZIP_FILE" .

echo gunzip -f "$GZIP_FILE"
gunzip -f "$GZIP_FILE"


if ! [[ -f $DUMP_FILE ]]
then 
 
  echo nema "$DUMP_FILE !?"
  echo " "
 
exit 1

else
  echo "koristim $DUMP_FILE"
  echo ""
fi

C_DIR=`pwd`
echo $C_DIR

echo ""
echo "radim restore iz dumpa"
echo ""


echo "starting pg_restore ..."

if [ "$HOST_NAME" == "" ] 
then
	pg_restore --dbname="$DB_NAME" "$DUMP_FILE"


else

	if [[ "$PGPASSWORD" == "" ]]
	then
	    echo "unesi $P_USER password:"
	    PWD_SWITCH=" -W "
	else
	    PWD_SWITCH=""
	fi


	echo pg_restore  --host $HOST_NAME --port $P_PORT --username $P_USER $PWD_SWITCH --dbname="$DB_NAME" "$DUMP_FILE"

	pg_restore --host $HOST_NAME --port $P_PORT --username $P_USER $PWD_SWITCH --dbname="$DB_NAME" "$DUMP_FILE"

fi

if [[ $? == 0 ]]
then
    echo "database $DB_NAME uspjesno vracen iz arhive"
    echo " "
else
   echo " "
   echo " "
   echo belaj ! neuspjesna komanda:
   echo $CMD
   exit 1
fi

echo " "
echo "------------------------"
echo "restore uspjesan :)"
echo "------------------------"
echo " "
exit 0


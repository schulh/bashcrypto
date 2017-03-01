#!/usr/bin/env bash

cyan='\e[0;36m'
green='\e[0;34m'
okegreen='\033[92m'
lightgreen='\e[1;32m'
white='\e[1;37m'
red='\e[1;31m'
yellow='\e[1;33m'
BlueF='\e[1;34m'

CIPHERMODE="aes-256-cbc"
SHA='sha256sum'
#$TARMODE=0

 spinner() {
    #local pid=$$
    local process='openssl'
    local delay=0.50
    local spinstr='/-\|'
	while [ "$(ps a | awk '{print $5}' | grep $process)" ]; do
        local temp=${spinstr#?}
        printf " [%c]  " "${spinstr}"
        local spinstr=${temp}${spinstr%"$temp}"}
        sleep ${delay}
        printf "\b\b\b\b\b\b"
    done
    printf "    \b\b\b\b"
}

function usage() {

    echo -e $cyan
	echo "Usage: ./bashcrypto [OPTION] -i INPUT -o OUTPUT"
	echo "-p	use public/privatekey for encryption/decryption"
	echo "-e	encrypt"
	echo "-d	decrypt"
	echo "-i	input"
	echo "-o	output"
	echo "-s	create shasum"
	echo "-t	directory will be archived"
	echo "-h	help message"
	echo "Exampels:"
	echo "./bashcrypto -e -s -p publickey.pem -i secretfile.txt -o foofile"
	echo "./bashcrypto -e -i secretfile.txt -o foofile"
	echo "./bashcrypto -d -p privatekey.pem -i foofile -o secretfile.txt"
	echo "./bashcrypto -d -i foofile -o secretfile.txt"
    echo ""
}



function encrypt() {
    echo -e $okegreen
    echo "[+] ENCRYPT"
    echo "[+] INPUT:  "$INPUT_FILE
    echo "[+] OUTPUT: "$OUTPUT_FILE
    echo ""
    echo -e $red
    echo "[!] Your file will be encypted now!"
	 
    echo ""
    # If key is given, use it
	if [[ $DEMODE -eq 1 ]]; then
        if [[ -f $INPUT_FILE ]]; then
        	openssl smime -encrypt -binary -aes-256-cbc -in $INPUT_FILE -out $OUTPUT_FILE -outform DER $KEYFILE | spinner
			if [[ $SHASUM == 1 ]]; then
				echo $($SHA $OUTPUT_FILE) > shasum.$OUTPUT_FILE.txt
			fi
		# If input is a directory and 'tarmode' is set to 0, encrypt every file in it
        elif [[ -d $INPUT_FILE && -z $TARMODE ]]; then
            DIR=$INPUT_FILE/*
            for file in $DIR; do
				openssl smime -encrypt -binary -aes-256-cbc -in $file -out $file.$OUTPUT_FILE -outform DER $KEYFILE | spinner
				 
				if [[ $SHASUM == 1 ]]; then
					echo $($SHA $OUTPUT_FILE) > shasum.$OUTPUT_FILE.txt
				fi
            done
		# If input is a directory and 'tarmode' is set to 1, create tar.gz and encrypt it
        elif [[ -d $INPUT_FILE && $TARMODE == 1 ]]; then
			tar -czf $OUTPUT_FILE.tar.gz $INPUT_FILE
        	openssl smime -encrypt -binary -aes-256-cbc -in $OUTPUT_FILE.tar.gz -out $OUTPUT_FILE -outform DER $KEYFILE | spinner
			 
			if [[ $SHASUM == 1 ]]; then
				echo $($SHA $OUTPUT_FILE) > shasum.$OUTPUT_FILE.txt
			fi
			rm $OUTPUT_FILE.tar.gz
		fi
    else
        if [[ -f $INPUT_FILE ]]; then
            openssl $CIPHERMODE -a -salt -in $INPUT_FILE -out $OUTPUT_FILE
				if [[ $SHASUM == 1 ]]; then
					echo $($SHA $OUTPUT_FILE) > shasum.$OUTPUT_FILE.txt
				fi
        elif [[ -d $INPUT_FILE && -z $TARMODE ]]; then
            DIR=$INPUT_FILE/*
            for file in $DIR; do
                openssl $CIPHERMODE -a -salt -in $file -out $file.$OUTPUT_FILE
				 
				if [[ $SHASUM == 1 ]]; then
					echo $($SHA $OUTPUT_FILE) > shasum.$file.$OUTPUT_FILE.txt
				fi
            done
		# If input is a directory and 'tarmode' is set to 1, create tar.gz and encrypt it
        elif [[ -d $INPUT_FILE && $TARMODE == 1 ]]; then
			tar -czf $OUTPUT_FILE.tar.gz $INPUT_FILE
            openssl $CIPHERMODE -a -salt -in $OUTPUT_FILE.tar.gz -out $OUTPUT_FILE
			 
			if [[ $SHASUM == 1 ]]; then
				echo $($SHA $OUTPUT_FILE) > shasum.$OUTPUT_FILE.txt
			fi
			rm $OUTPUT_FILE.tar.gz
		fi
    fi
    echo -e $okegreen
    echo "[+] Done!"
}

function decrypt() {
    echo -e $okegreen
    echo "[+] DECRYPT"
    echo "[+] INPUT:  "$INPUT_FILE
    echo "[+] OUTPUT: "$OUTPUT_FILE
    if [[ $DEMODE -eq 1 ]]; then
        if [[ -f $INPUT_FILE ]]; then
	     openssl smime -decrypt -in $INPUT_FILE -binary -inform DEM -inkey $KEYFILE -out $OUTPUT_FILE
			if [[ $TARMODE == 1 ]]; then
				tar -xvf $OUTPUT_FILE
			fi
        elif [[ -d $INPUT_FILE ]]; then
            DIR=$INPUT_FILE/*
            for file in $DIR; do
				openssl smime -decrypt -in $file -binary -inform DEM -inkey $KEYFILE -out $file.$OUTPUT_FILE
				if [[ $TARMODE == 1 ]]; then
					tar -xvf $OUTPUT_FILE
				fi
            done
        fi
    else
        if [[ -f $INPUT_FILE ]]; then
            openssl $CIPHERMODE -d -a -in $INPUT_FILE -out $OUTPUT_FILE
				if [[ $TARMODE == 1 ]]; then
					tar -xvf $OUTPUT_FILE
				fi
        elif [[ -d $INPUT_FILE ]]; then
            DIR=$INPUT_FILE/*
            for file in $DIR; do
                openssl $CIPHERMODE -d -a -in $file -out $file.$OUTPUT_FILE
				if [[ $TARMODE == 1 ]]; then
					tar -xvf $OUTPUT_FILE
				fi
            done
        fi
    fi
}


while getopts "stedi:o:p:Hh" opt;do
    case $opt in

    i)
        INPUT_FILE=$OPTARG
        if [[ -f $INPUT_FILE ]] ; then
			echo -e $okgreen
       		echo "[+] inputfile found!"
            echo ""
        elif [[ -d $INPUT_FILE ]] ; then
        	echo -e $okgreen
            echo "[+] directory found!"
            echo ""
			DIRMODE=1
        else
			echo -e $red
            echo "[!] INPUT NOT FOUND!"
            exit 1
            echo ""
        fi
        ;;
    e)
        MODE=1;;
	t)
		TARMODE=1;;
    d)
        MODE=2;;
    o)
        OUTPUT_FILE=$OPTARG;;
	s)
		SHASUM=1;;
    p)
        KEYFILE=$OPTARG
        DEMODE=1
        if [[ ! -f $KEYFILE ]]; then
            echo -e $red
            echo "Keyfile not found"
            echo ""
            exit -1
        fi;;
	H|h)
                usage
        exit -1;;
	\?)
                usage
        exit -1;;
   esac
done



if [[ $MODE -eq 1 ]]; then
    encrypt
elif [[ $MODE -eq 2 ]]; then
    decrypt
else
    echo -e $red "FAILURE"
fi

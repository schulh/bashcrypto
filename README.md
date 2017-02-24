# bashcrypto
## Usage
### Create public/private key
```bash
# Create public/private key
openssl req -x509 -nodes -newkey rsa:4096 -keyout privatekey.pem -out publickey.pem
# Create public/private key with password protection
openssl req -x509 -newkey rsa:4096 -keyout privatekey.pem -out publickey.pem
```
### Encrypt
```bash
# encrypt with a password:
./bashcrypto -e -i test.txt -o test.txt.enc
# encrypt all files in a folder
./bashcrypto -e -i dir -o enc
# encrypt with your public key:
./bashcrypto -e -p public.pem -i test.txt -o ultrasecret.dat
# encrypt all files in a folder
./bashcrypto -e -p public.pem -i dir -o ultrasecret.dat
```
### Decrypt
```bash
# decrypt with password:
./bashcrypto -d -i test.txt.enc -o plain.txt
# decrypt with private key:
./bashcrypto -d -p private.pem -i ultrasecret.dat -o plain.txt
```

## To-Do
- create shasum
- tar
- ?

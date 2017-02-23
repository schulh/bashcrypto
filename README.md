# bashcrypto
## Usage
### Create public/private key
```bash
# Create private key
openssl genrsa -aes256 -out private.pem 4096
# generate public key
openssl rsa -in private.pem -outform PEM -pubout -out public.pem
```
### Encrypt
```bash
# encrypt with a password:
./bashcrypto -e -i test.txt -o test.txt.enc
# encrypt with your public key:
./bashcrypto -e -p public.pem -i test.txt -o ultrasecret.dat
```
### Decrypt
```bash
# decrypt with password:
./bashcrypto -d -i test.txt.enc -o plain.txt
# decrypt with private key:
./bashcrypto -d -p private.pem -i ultrasecret.dat -o plain.txt
```

# bashcrypto
## Usage
### Encrypt
```bash
# To encrypt with a password:
./bashcrypto -e -i test.txt -o test.txt.enc
# To encrypt with your public key:
./bashcrypto -e -p public.pem -i test.txt -o ultrasecret.dat
```
### Decrypt
```bash
./bashcrypto -d -i test.txt.enc -o plain.txt
./bashcrypto -d -p private.pem -i ultrasecret.dat -o plain.txt
```

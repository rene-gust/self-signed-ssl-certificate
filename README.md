# self-signed-ssl-certificate
documenting how to create a self signed ssl certificate
@see https://help.ubuntu.com/lts/serverguide/certificates-and-security.html
@see https://stackoverflow.com/questions/43665243/chrome-invalid-self-signed-ssl-cert-subject-alternative-name-missing

change www.lsk.loc to your domain in v3.ext and all commands

```
openssl genrsa -des3 -out server.key 2048
openssl rsa -in server.key -out server.key.insecure
mv server.key server.key.secure
mv server.key.insecure server.key
openssl req -new -key server.key -out server.csr
openssl x509 -req -days 365 -in server.csr -signkey server.key -sha256 -extfile v3.ext -out server.crt
sudo cp server.crt /etc/ssl/certs
sudo cp server.key /etc/ssl/private
sudo mkdir /etc/ssl/CA
sudo mkdir /etc/ssl/newcerts
sudo sh -c "echo '01' > /etc/ssl/CA/serial"
sudo touch /etc/ssl/CA/index.txt
```

edit /etc/ssl/openssl.cnf and change the values like in ./openssl.cnf

```
openssl req -new -x509 -extensions v3_ca -keyout cakey.pem -out cacert.pem -days 3650
sudo mv cakey.pem /etc/ssl/private/
sudo mv cacert.pem /etc/ssl/certs/
sudo openssl ca -in server.csr -config /etc/ssl/openssl.cnf
sudo cp /etc/ssl/newcerts/01.pem /etc/ssl/newcerts/www.lsk.loc.crt
```


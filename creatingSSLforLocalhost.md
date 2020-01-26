# Creating and adding custom SSL for your localhost
<table>
  <tr>
    <th>SNo</th>
    <th>Contents</th>
  </tr>
  <tr>
    <th colspan="2">Create a Root Secure Sockets Layer (SSL) certificate</th>
  </tr>
  <tr>
    <td>1</td>
    <td>Create a Root Secure Sockets Layer (SSL) certificate</td>
  </tr>
  <tr>
    <td>2</td>
    <td>Create a root key</td>
  </tr>
  <tr>
    <td>3</td>
    <td>Add this certificate to Linux ca-certificates</td>
  </tr>
  <tr>
    <td>4</td>
    <td>Create domain SSL certificate</td>
  </tr>
  <tr>
    <th colspan="2">Host your SSL certificate on localhost</th>
  </tr>
  <tr>
    <td>1</td>
    <td>Put your SSL files in apache web server</td>
  </tr>
  <tr>
    <td>2</td>
    <td>Add virtual host with paths to SSL certificate in the configuration file</td>
  </tr>
  <tr>
    <td>3</td>
    <td>Restart the web server</td>
  </tr>
  <tr>
    <th colspan="2">File structures</th>
  </tr>
  <tr>
    <td>1</td>
    <td>.ext file structure - v3.ext</td>
  </tr>
  <tr>
    <td>2</td>
    <td>.cnf file structure - server.csr.cnf</td>
  </tr>
</table>

## Create a Root Secure Sockets Layer (SSL) certificate
This root certificate can then be used to sign any number of certificates you might generate for individual domains<br>
`openssl genrsa -des3 -out rootCA.key 2048`<br>
This generate a RSA-2048 key and save it to a file rootCA.key. The pass phrase used to generate a certificate will be used to needed every time we create a new certificate

## Create a root key
We can use the key we generated to create a new Root SSL certificate. Save it to a file named rootCA.pem. We can also mention the validity of certificate in days by putting it in place of &lt;no-of-days&gt;<br>
`openssl req -x509 -new -nodes -key rootCA.key -sha256 -days <no-of-days> -out rootCA.pem`

### Convert .pem to .crt
Convert this root key to from .pem to .crt for Linux distribution<br>
`openssl x509 -in rootCA.pem -inform PEM -out rootCA.crt`

## Add this certificate to Linux ca-certificates
### Directory for ca-certificates
Create a directory for your-certificates CA certificates in /usr/share/ca-certificates:<br>
`sudo mkdir /usr/share/ca-certificates/my-certificates`
### Copy the CA .crt file to this directory:
`sudo cp rootCA.crt /usr/share/ca-certificates/my-certificates/rootCA.crt`
### Update CA-Certificates
Let Ubuntu add the .crt file's path relative to /usr/share/ca-certificates to /etc/ca-certificates.conf:<br>
`sudo dpkg-reconfigure ca-certificates`<br>
To do this non-interactively, run:<br>
`sudo update-ca-certificates`

## Domain SSL certificate
### Create key and csr
`openssl req -new -sha256 -nodes -out <your-domain>.csr -newkey rsa:2048 -keyout <your-domain>.key -config <a .csr.conf file>`
### Create crt
`openssl x509 -req -in server.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out <your-domain>.crt -days 18250 -sha256 -extfile <a .ext file>.ext`
### Create cs-bundle
`cat <your domain crt file> <root crt file> > <your-domain>.ca-bundle`

## Host these SSL certificates
When your SSL files are ready, do the following:
1. Create a folder on your server to store the SSL files. For example, you can use this directory: `xampp\apache\ssl`
1. Use the file explorer to locate the configuration file. For example, `d\xampp\apache\conf\extra\httpd-ssl.conf`
1. Open the configuration file with any text editor such as Notepad, and edit the virtual host for port 443. Follow the example below, then save the Changes.<br>
`<virtualhost *:443="">`<br>
    `DocumentRoot "/var/www"`<br>
    `ServerName yourdomain.com`<br>
    `ServerAlias www.yourdomain.com`<br>
    `SSLEngine on`<br>
    `SSLCertificateFile "D:/xampp/apache/ssl/yourdomain_com.crt"`<br>
    `SSLCertificateKeyFile "D:/xampp/apache/ssl/yourdomain_com.key"`<br>
    `SSLCACertificateFile "D:/xampp/apache/ssl/yourdomain_com.ca-bundle"`<br>
`</virtualhost>`<br>
Note: Make sure you replace the server name and alias, and the certificate paths with your actual names and locations.
1. Restart the server

Note: Relative to XAMPP web server. Locate files according to your distribution.<br>For eg, apache web servers in Ubuntu have SSL configuration file at `/etc/apache2/sites-available/default-ssl.conf` and for storing SSL files, I would suggest using `/etc/apache2/ssl`

## File structure of ext and conf files
You will need these files for SSL creation.
### v3.ext
    authorityKeyIdentifier=keyid,issuer
    basicConstraints=CA:FALSE
    keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
    subjectAltName = @alt_names

    [alt_names]
    DNS.1 = localhost
    IP.1 = 127.0.0.1
    IP.2 = 0.0.0.0
### server.csr.cnf
    [req]
    default_bits = 2048
    prompt = no
    default_md = sha256
    distinguished_name = dn

    [dn]
    C=IN
    ST=Delhi
    emailAddress=faisalakhtar1999@gmail.com
    CN = localhost

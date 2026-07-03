# Host setup

## Use

**Install docker**

See: https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script

```bash
curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh
```

**Add users**
```bash
sudo usermod -aG docker $USER
sudo usermod -aG docker netadmin
```
Start a new session (log out & log back in) to activate the new group membership

**Build our docker bridge, environment variables, folders, & set permissions**
Note the bridge name dbr0 is also inserted into the environment variables

```bash
docker network create --driver=bridge --subnet=10.42.0.0/24 --gateway=10.42.0.1 dbr0
sudo mkdir -p /opt/{docker,stacks}
sudo chown $USER:$USER /opt/docker
sudo chown $USER:$USER /opt/stacks
sudo tee -a /etc/environment << 'EOF'
PUID=1000
PGID=1000
TZ=America/Los_Angeles
LOCVOL1=/opt/docker/
CONNET=dbr0
EOF
```





<details>

<summary>Full use</summary>

## Old way


**Clone git to system**
```bash
rm -fr ~/appdata/stacks/hawser
mkdir -p ~/appdata/hawser/
git clone https://github.com/FinchTechSoCal/docker-hawser.git ~/appdata/stacks/hawser
sed -i 's;/path/to/appdata/;'$HOME'/appdata/;g' ~/appdata/stacks/hawser/.env
sed -i 's;AGENT_NAME=;AGENT_NAME='$(cat /etc/hostname)';g' ~/appdata/stacks/hawser/.env
```

**Select either option**

*Edge mode*:
These are generated from dockhand

```bash
DOCKHAND_SERVER_URL=Your_Dockhand_WSS_URL
TOKEN=Your_Generated_Token
```
Then
```bash
sed -i 's;DOCKHAND_SERVER_URL=;DOCKHAND_SERVER_URL='$DOCKHAND_SERVER_URL';g' ~/appdata/stacks/hawser/.env
sed -i 's;YourOwnSuperSecretToken;'$TOKEN';g' ~/appdata/stacks/hawser/.env
```

*Standalone mode*:
```bash
TOKEN=$(openssl rand -base64 32)
openssl ecparam -name prime256v1 -genkey -noout -out ~/appdata/stacks/hawser/server.key
openssl req -new -x509 -days 3652 -key ~/appdata/stacks/hawser/server.key -sha256 -out ~/appdata/stacks/hawser/server.crt -subj "/C=US/ST=California/CN=hawser"
sed -i 's;YourOwnSuperSecretToken;'$TOKEN';g' ~/appdata/stacks/hawser/.env
sed -i 's;TLS_CERT=;TLS_CERT=/cert/server.crt;g' ~/appdata/stacks/hawser/.env
sed -i 's;TLS_KEY=;TLS_KEY=/cert/server.key;g' ~/appdata/stacks/hawser/.env
```

**Run**
```bash
docker compose -f ~/appdata/stacks/hawser/docker-compose.yml up -d
```

---


Get token & connection string from dockhand and set variables:
```bash
DOCKHAND_SERVER_URL=Your_Dockhand_WSS_URL
TOKEN=Your_Generated_Token
```

Or self generate the token (for Standard Mode)
```bash
TOKEN=$(openssl rand -base64 32)
```

**Pull & Update .env**

```bash
rm -fr ~/appdata/stacks/hawser
mkdir -p ~/appdata/hawser/
git clone https://github.com/FinchTechSoCal/docker-hawser.git ~/appdata/stacks/hawser
sed -i 's;/path/to/appdata/;'$HOME'/appdata/;g' ~/appdata/stacks/hawser/.env
sed -i 's;DOCKHAND_SERVER_URL=;DOCKHAND_SERVER_URL='$DOCKHAND_SERVER_URL';g' ~/appdata/stacks/hawser/.env
sed -i 's;YourOwnSuperSecretToken;'$TOKEN';g' ~/appdata/stacks/hawser/.env
sed -i 's;AGENT_NAME=;AGENT_NAME='$(cat /etc/hostname)';g' ~/appdata/stacks/hawser/.env
openssl ecparam -name prime256v1 -genkey -noout -out ~/appdata/stacks/hawser/server.key
openssl req -new -x509 -days 3652 -key ~/appdata/stacks/hawser/server.key -sha256 -out ~/appdata/stacks/hawser/server.crt -subj "/C=US/ST=California/CN=hawser"
```

**Self-signed RSA cert**
```bash
openssl req -x509 -newkey rsa:2048 -keyout ~/appdata/hawser/server.key -out ~/appdata/hawser/server.crt -sha256 -days 3650 -subj "/C=US/ST=California/CN=hawser.io" -nodes
```

**Run**
```bash
docker compose -f ~/appdata/stacks/hawser/docker-compose.yml up -d
```




**Modify .env**
```bash
nano ~/appdata/stacks/hawser/.env
```

**Generate RSA Keypair**
```bash
openssl req -x509 -newkey rsa:2048 -keyout ~/appdata/hawser/server.key -out ~/appdata/hawser/server.crt -sha256 -days 3650 -subj "/C=US/ST=California/CN=hawser.io" -nodes
```

```bash
openssl req -x509 -newkey rsa:2048 -keyout server.key -out server.crt -sha256 -days 365 -subj "/C=US/ST=State/L=City/O=Organization/CN=yourdomain.com" -nodes
```

**Generate ECDSA Keypair**

https://www.google.com/search?q=ubuntu+self+signed+certificate+ECDSA&oq=ubuntu+self+signed+certificate+ECDSA

```bash
openssl ecparam -name prime256v1 -genkey -noout -out server.key
openssl req -new -x509 -days 3652 -key server.key -sha256 -out server.crt
```
Or 1 line:
```bash
openssl ecparam -name prime256v1 -genkey -noout -out ~/appdata/hawser/server.key | openssl req -new -x509 -days 3652 -key ~/appdata/hawser/server.key -sha256 -out ~/appdata/hawser/server.crt

```

https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/configuring-https-ssl.html
https://arminreiter.com/2022/01/create-your-own-certificate-authority-ca-using-openssl/
https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/securing_networks/creating-and-managing-tls-keys-and-certificates_securing-networks#creating-a-private-ca-using-openssl_creating-and-managing-tls-keys-and-certificates

</details>

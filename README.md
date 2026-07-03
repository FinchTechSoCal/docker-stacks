# docker-stacks
Docker stacks

Current (new) docker stacks location

## Host setup

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

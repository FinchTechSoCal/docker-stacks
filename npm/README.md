## Use

Trying without the external MySQL database

**Clone git to system**
```bash
rm -fr ~/appdata/stacks/npm
mkdir -p ~/appdata/npm/
git clone https://github.com/FinchTechSoCal/docker-stack.git ~/appdata/stacks/npm
sed -i 's;/path/to/appdata/;'$HOME'/appdata/;g' ~/appdata/stacks/npm/.env
```

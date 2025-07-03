# W. M. Keck Observatory - Grafana data source plugin for Keywords
#### Update July 2025 - Paul Richards

This data source is a refreshed version of the original Keck Observatory Grafana data source plugin for Keywords. 
It has been updated to work with the latest Grafana versions (v12) and uses the latest Grafana plugin SDK for Go.

### Install Grafana
```
apt install -y adduser libfontconfig1 musl
wget https://dl.grafana.com/enterprise/release/grafana-enterprise_11.4.0_amd64.deb
dpkg -i grafana-enterprise_11.4.0_amd64.deb

systemctl daemon-reload
systemctl enable grafana-server
systemctl start grafana-server
```
### Verify Grafana is running, check http://localhost:3000

```
systemctl stop grafana-server
```

### Setup the Grafana user
```
chsh grafana
# /bin/bash
chown -R grafana /usr/share/grafana
```

### Install Go
```
wget https://go.dev/dl/go1.23.4.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.23.4.linux-amd64.tar.gz
```

### Add to /etc/profile
```
export PATH=$PATH:/usr/local/go/bin
```

### Add this to ~/.profile
```
export PATH=$PATH:/usr/local/go/bin:~/go/bin
```

### Test
```
go version
```

### Install Node Version Manager
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
```

### Download and install Node.js (you may need to restart the terminal)
```
nvm install 23
```

### Verify the right Node.js version is in the environment
```
node -v # should print something like `v23.4.0`
```

### verifies the right npm version is in the environment
```
npm -v # should print something like `10.9.2`
```

### Install Mage
```
go install github.com/magefile/mage@latest
mage -init
```

### Install the data source plugin
```
su - grafana
mkdir /var/lib/grafana/plugins
cd /var/lib/grafana/plugins
git clone https://github.com/KeckObservatory/wmko-keyword-datasource
```

### Build keyword datasource
```
cd /var/lib/grafana/plugins/wmko-keyword-datasource
npm install
npm run typecheck
npm run dev
# hit Ctrl-C once the screen stops updating

go get github.com/lib/pq
mage -v build:linux

```

### Modify Grafana to see plugin
```
vi /etc/grafana/grafana.ini

# locate this line:
;allow_loading_unsigned_plugins
# change it to:
allow_loading_unsigned_plugins = wmko-keyword-datasource

(as root)
systemctl restart grafana-server
```

## Learn more

Below you can find source code for existing app plugins and other related documentation.

- [Basic data source plugin example](https://github.com/grafana/grafana-plugin-examples/tree/master/examples/datasource-basic#readme)
- [`plugin.json` documentation](https://grafana.com/developers/plugin-tools/reference/plugin-json)
- [How to sign a plugin?](https://grafana.com/developers/plugin-tools/publish-a-plugin/sign-a-plugin)

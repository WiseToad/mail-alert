The simple and small Python-based mail alerter.

## PREREQUISITES

Debian:
```sh
sudo apt install -y python3 python3-venv pip
```

Fedora:
```sh
sudo dnf install -y python3 pip
```

## INSTALL

```sh
mkdir -p ~/mail-alert
cd ~/mail-alert

wget https://github.com/WiseToad/mail-alert/releases/latest/download/mail-alert.tar.gz

sudo mkdir -p /opt/mail-alert
sudo tar xzf mail-alert.tar.gz -C /opt/mail-alert

sudo ln -s /opt/mail-alert/bin/mail-alert /usr/local/bin

sudo useradd -r -d /opt/mail-alert -s /usr/sbin/nologin mail-alert
sudo chgrp mail-alert /opt/mail-alert/bin/mail-alert
sudo chgrp mail-alert /opt/mail-alert/config/mail-alert.conf

cd /opt/mail-alert
sudo python3 -m venv venv
sudo bash -c "source venv/bin/activate && pip install -r requirements.txt"
```

## CONFIGURE

- specify SMTP parameters in config file:
```sh
sudo mcedit /opt/mail-alert/config/mail-alert.conf
```

- configure systemd helper service:
```sh
sudo mcedit /etc/systemd/system/alert@.service
```	
```
[Unit]
Description=Service status alert
Wants=network-online.target
After=network-online.target

[Service]
Type=oneshot
User=mail-alert
ExecStart=bash -c "systemctl status \"%i\" | mail-alert \"Status alert, host: %H, service: %i\""
```	
```sh
sudo systemctl daemon-reload
```

## USAGE

- to configure failure alerting in some systemd service, add the following:
```
[Unit]
...
OnFailure=alert@.service
```

- to fire some arbitrary alert, launch:
```sh
echo "Alert text" | sudo mail-alert "Alert subject" ["recipient@example.com" ...]
```

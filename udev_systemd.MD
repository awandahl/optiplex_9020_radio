
### /etc/udev/rules.d

99-radio-devices.rules
```ini
# QMX Radio
ACTION=="add", SUBSYSTEM=="tty", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="a34c", SYMLINK+="ttyQMX", MODE="0666", GROUP="dialout"

# Winkeyer Large Keyer
# ACTION=="add", SUBSYSTEM=="tty", ATTRS{idVendor}=="1a86", ATTRS{idProduct}=="7523", SYMLINK+="ttyWinkeyer", MODE="0666", GROUP="dialout"

# K3NG keyer for cwdaemon
ACTION=="add", SUBSYSTEM=="tty", ATTRS{idVendor}=="1a86", ATTRS{idProduct}=="7523", SYMLINK+="ttyCW", MODE="0666", GROUP="dialout"

# Elecraft K2
ACTION=="add", SUBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", SYMLINK+="ttyK2", MODE="0666", GROUP="dialout"

# mcHF
ACTION=="add", SUBSYSTEM=="tty", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="5732", SYMLINK+="ttymcHF", MODE="0666", GROUP="dialout"

```
# udev

#### Basic udev commands

| Command | Purpose |
| :-- | :-- |
| `udevadm control --reload` | Reload udev rules files |
| `udevadm trigger` | Re-evaluate all present devices |
| `udevadm trigger --subsystem-match=usb` | Re-evaluate only USB devices |
| (Unplug/replug device) | Physically re-trigger rule for device |

**You do NOT need to reboot after updating udev rules**—these commands are sufficient for almost all changes to `/etc/udev/rules.d/`.

### /etc/systemd/system

rigctld-k2.service
```ini
[Unit]
Description=Rigctld for Elecraft K2 Radio
After=network.target

[Service]
User=aw
Group=aw
ExecStart=/usr/local/bin/rigctld \
  -m 2021 \
  -r /dev/ttyK2 \
  -s 4800 \
  -t 4532
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

rigctld-qmx.service
```ini
[Unit]
Description=Rigctld for QMX Radio
After=network.target

[Service]
User=aw
Group=aw
ExecStart=/usr/local/bin/rigctld \
# TS480
#  -m 2028 \
# QMX latest
#  -m 2057 \
# QCX/QDX
  -m 2052 \
# TS440
#  -m 2002 \
  -r /dev/ttyQMX \
  -s 115200 \
  -t 4533
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

rigctld-mchf.service
```ini
[Unit]
Description=Rigctld for mcHF Radio
After=network.target

[Service]
User=aw
Group=aw
ExecStart=/usr/local/bin/rigctld \
# FT-817 works!
  -m 1020 \
# mcHF seems not to work!
# -m 1045 \
  -r /dev/ttymcHF \
  -s 38400 \
  -t 4534
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

cwdaemon.service (default port 6789)
```ini
[Unit]
Description=CWdaemon service for keying
After=dev-ttyS0.device
Requires=dev-ttyS0.device

[Service]
Type=forking
User=aw
Group=aw
ExecStart=/usr/sbin/cwdaemon -d ttyS0 -x n
Restart=always
RestartSec=5
Environment="PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin"

[Install]
WantedBy=multi-user.target
```

# systemctl

#### Basic systemctl Commands

| Command | Description |
| :-- | :-- |
| systemctl daemon-reload | Instructs systemd to reload its configuration files |
| systemctl status [service] | See status of a service (running, errors, logs) |
| systemctl start [service] | Start a service NOW |
| systemctl stop [service] | Stop a service |
| systemctl restart [service] | Restart (stop \& start) a service |
| systemctl reload [service] | Reload config of a service (if supported) |
| systemctl enable [service] | Enable service to start at boot |
| systemctl disable [service] | Disable service from starting at boot |
| systemctl is-active [service] | Is the service running now? |
| systemctl is-enabled [service] | Is the service enabled to autostart at boot? |
| systemctl list-units --type=service | List all currently loaded/running services |
| systemctl list-unit-files --type=service | List all services (enabled/disabled/etc) |

### System Power Commands

| Command | Description |
| :-- | :-- |
| systemctl reboot | Reboot the system |
| systemctl poweroff | Shut down the system |
| systemctl suspend | Suspend (sleep) the system |
| systemctl hibernate | Hibernate the system |
| systemctl halt | Halt the system |


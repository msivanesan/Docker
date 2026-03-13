# Linux Systemd Service Cheat Sheet (Key-Value Reference)

This guide lists the **most commonly used systemd service directives** used when creating services.

---

# 1. Service File Location

```
/etc/systemd/system/
```

Create service:

```bash
sudo nano /etc/systemd/system/myservice.service
```

---

# 2. Basic Service Template

```ini
[Unit]
Description=My Custom Service
Documentation=https://example.com/docs
After=network.target
Requires=network.target

[Service]
Type=simple
User=root
Group=root
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/myapp
ExecStop=/bin/kill $MAINPID
Restart=always
RestartSec=5
Environment=ENV=production
EnvironmentFile=/etc/myapp/env

[Install]
WantedBy=multi-user.target
```

---

# 3. [Unit] Section Keys

Defines **metadata and dependencies**.

| Key | Description |
|----|-------------|
| Description | Human readable service name |
| Documentation | Documentation URL |
| Requires | Hard dependency |
| Wants | Soft dependency |
| Before | Start before another unit |
| After | Start after another unit |
| Conflicts | Cannot run with another unit |
| ConditionPathExists | Run only if file exists |
| ConditionPathIsDirectory | Run only if directory exists |
| ConditionKernelCommandLine | Check kernel parameters |
| StartLimitIntervalSec | Rate limit restart attempts |
| StartLimitBurst | Max restart attempts |

Example:

```ini
[Unit]
Description=Example Service
After=network.target
Requires=mysql.service
```

---

# 4. [Service] Section Keys

Defines **how the service runs**.

## Service Type

| Key | Description |
|----|-------------|
| Type=simple | Default service |
| Type=forking | Background daemon |
| Type=oneshot | Run once and exit |
| Type=notify | Service notifies systemd |
| Type=idle | Start after other jobs |

Example:

```ini
Type=simple
```

---

## User and Permissions

| Key | Description |
|----|-------------|
| User | User running service |
| Group | Group running service |
| SupplementaryGroups | Additional groups |

Example:

```ini
User=appuser
Group=appgroup
```

---

## Execution Commands

| Key | Description |
|----|-------------|
| ExecStart | Main command |
| ExecStartPre | Run before service starts |
| ExecStartPost | Run after service starts |
| ExecReload | Command for reload |
| ExecStop | Command to stop |
| ExecStopPost | Command after stop |

Example:

```ini
ExecStartPre=/bin/mkdir -p /var/log/myapp
ExecStart=/usr/bin/node server.js
ExecStop=/bin/kill $MAINPID
```

---

## Restart Options

| Key | Description |
|----|-------------|
| Restart=no | Never restart |
| Restart=always | Always restart |
| Restart=on-failure | Restart on crash |
| Restart=on-abnormal | Restart if abnormal |
| Restart=on-success | Restart if success |
| RestartSec | Delay before restart |

Example:

```ini
Restart=on-failure
RestartSec=5
```

---

## Environment Variables

| Key | Description |
|----|-------------|
| Environment | Define variable |
| EnvironmentFile | Load env file |

Example:

```ini
Environment=PORT=8080
Environment=NODE_ENV=production
EnvironmentFile=/etc/myapp.env
```

---

## Working Directory

| Key | Description |
|----|-------------|
| WorkingDirectory | Service working directory |

Example:

```ini
WorkingDirectory=/var/www/app
```

---

## Logging

| Key | Description |
|----|-------------|
| StandardOutput | stdout destination |
| StandardError | stderr destination |
| SyslogIdentifier | Log identifier |

Example:

```ini
StandardOutput=journal
StandardError=journal
SyslogIdentifier=myapp
```

---

## Resource Limits

| Key | Description |
|----|-------------|
| LimitNOFILE | Max open files |
| LimitNPROC | Max processes |
| MemoryLimit | Memory limit |
| CPUQuota | CPU limit |

Example:

```ini
LimitNOFILE=65535
MemoryLimit=500M
CPUQuota=50%
```

---

## Security Options

| Key | Description |
|----|-------------|
| PrivateTmp | Isolate /tmp |
| ProtectSystem | Protect system dirs |
| ProtectHome | Protect /home |
| NoNewPrivileges | Prevent privilege escalation |
| CapabilityBoundingSet | Limit Linux capabilities |

Example:

```ini
PrivateTmp=true
ProtectSystem=full
NoNewPrivileges=true
```

---

# 5. [Install] Section Keys

Defines **how service integrates with boot targets**.

| Key | Description |
|----|-------------|
| WantedBy | Target that starts service |
| RequiredBy | Hard dependency target |
| Also | Install additional units |
| Alias | Alternate name |

Example:

```ini
[Install]
WantedBy=multi-user.target
```

---

# 6. Common Targets

| Target | Meaning |
|------|--------|
| multi-user.target | Normal server mode |
| graphical.target | Desktop mode |
| network.target | Network ready |
| rescue.target | Rescue mode |

---

# 7. Service Lifecycle Commands

Reload systemd:

```bash
sudo systemctl daemon-reload
```

Start service:

```bash
sudo systemctl start myservice
```

Stop service:

```bash
sudo systemctl stop myservice
```

Restart service:

```bash
sudo systemctl restart myservice
```

Enable at boot:

```bash
sudo systemctl enable myservice
```

Disable service:

```bash
sudo systemctl disable myservice
```

Check status:

```bash
sudo systemctl status myservice
```

---

# 8. Logs

View logs:

```bash
journalctl -u myservice
```

Live logs:

```bash
journalctl -u myservice -f
```

---

# 9. Debugging Failed Service

Check errors:

```bash
systemctl status myservice
journalctl -xe
```

---

# 10. Production Service Example

```ini
[Unit]
Description=Node API Service
After=network.target

[Service]
Type=simple
User=nodeuser
WorkingDirectory=/var/www/api
ExecStart=/usr/bin/node server.js
Restart=on-failure
RestartSec=3
Environment=NODE_ENV=production
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```

---
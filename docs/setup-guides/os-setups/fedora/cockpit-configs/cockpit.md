# Stuff to remember

## Kernel Dump

```shell
sudo grubby --update-kernel ALL --args crashkernel=512M
```

## Session Recording

`journald` rotates and eventually removes old recordings according to its size and retention settings. They do not necessarily have a fixed expiration date unless you configure one.

Relevant controls include:

SystemMaxUse=: maximum persistent journal space
SystemKeepFree=: disk space journald must leave available
SystemMaxFileSize=: maximum size of each journal file
MaxRetentionSec=: maximum age of retained journal data

When space limits require cleanup, journald rotates files and deletes older historical data.

Since tlog recordings share the journal with other system logs, journald removes old data at the journal-file level, not by treating each recording as an independent file.

### Creating a retention policy

```shell
sudo mkdir -p /etc/systemd/journald.conf.d &&
sudo nano /etc/systemd/journald.conf.d/retention.conf
```

Add:

```toml
[Journal]
Storage=persistent
SystemMaxUse=2G
SystemKeepFree=2G
MaxRetentionSec=30day
Compress=yes
```

Create the persistent journal directory correctly and apply the configuration:

```shell
sudo mkdir -p /var/log/journal &&
sudo systemd-tmpfiles --create --prefix /var/log/journal &&
sudo systemctl restart systemd-journald &&
sudo journalctl --flush
```

Locate recordings:

```shell
sudo journalctl --disk-usage &&
sudo journalctl --header | head &&
ls -ld /var/log/journal
```

Show recent tlog entries:

```shell
sudo journalctl SYSLOG_IDENTIFIER=tlog-rec-session --since today
```

Follow recording messages live:

```shell
sudo journalctl -f SYSLOG_IDENTIFIER=tlog-rec-session
```

Read the entire journal directory:

```shell
sudo journalctl --directory=/var/log/journal/<journal-directory>
```

Show recordings from directory:

```shell
sudo journalctl \
  --directory=/var/log/journal/<journal-directory> \
  SYSLOG_IDENTIFIER=tlog-rec-session
```

Local overrides belong in `/etc/systemd/journald.conf.d/`, which has precedence over the vendor configuration.

Verify:

```shell
systemd-analyze cat-config systemd/journald.conf &&
sudo journalctl --disk-usage &&
sudo journalctl --list-boots
```

### Manual Cleanup

!!! warning

    These commands affect **all journal recordings** on
    `app01`, including service, kernel, authentication,
    and tlog session records.

To manually remove entries:

```shell
sudo journalctl --rotate &&
sudo journalctl --vacuum-time=30d
```

Or rotate based on size:

```shell
sudo journalctl --rotate &&
sudo journalctl --vacuum-size=2G
```

---
icon: lucide/server
---

# App Server First Boot

## Verify Storage

```shell
lsblk -f
sudo btrfs filesystem show
sudo btrfs subvolume list /
findmnt -t btrfs
```

Expected:

* `app01-system` present
* `/home` subvolume present
* `/var` subvolume present
* `/srv/apps` subvolume present
* `/srv/data` subvolume present
* No mount failures

---

## Verify Host Configuration

Hostname:

```shell
hostnamectl
```

Expected:

```text
app01
```

Time:

```shell
timedatectl
```

Expected:

* NTP synchronized
* Correct timezone

---

## Configure Networking

!!! warning

    You must have an active Domain Controller to point your dns
    entries at.

Verify interface:

```shell
ip addr
ip route
nmcli con show
```

Configure dc IP:

```shell
sudo nmcli con mod lan ipv4.method auto
sudo nmcli con mod lan ipv4.ignore-auto-dns yes
sudo nmcli con mod lan ipv4.dns-search "smith-lab.xyz"
sudo nmcli con mod lan ipv4.dns <dc-dns>
```

Apply:

```shell
sudo nmcli con down lan
sudo nmcli con up lan
```

Verify:

```shell
dig @192.168.0.10 dc01.smith-lab.xyz
dig dc01.smith-lab.xyz
host google.com
```

Expected:

* Queries are returned
* Host returns details for google.com

---

## Join Domain

Now that our network is pointing DNS at our Domain Controller, we need to join the domain.

For an in depth guide, please visit [Joining a Domain](../../../../samba/domain_join.md).

---

## Verify Cockpit

At this point, you should be set up to login from cockpit.

Enter in [https://app01:9090](https://app01:9090) in your browser to navigate to the login menu.

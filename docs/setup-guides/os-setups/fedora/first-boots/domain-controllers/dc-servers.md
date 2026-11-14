# Samba AD DC Provisioning

## Verify Storage

```shell
lsblk -f
sudo btrfs filesystem show
sudo btrfs subvolume list /
findmnt -t btrfs
```

Expected:

* rootfs present
* /home subvolume present
* /var subvolume present
* No mount failures

---

## Verify Host Configuration

Hostname:

```shell
hostnamectl
```

Expected:

```text
dc01
```

Time:

```shell
timedatectl
```

Expected:

* NTP synchronized
* Correct timezone

---

## Configure Static Networking

!!! warning

    Use upstream DNS until Samba DNS is validated.

Verify interface:

```shell
ip addr
ip route
nmcli con show
```

Configure static IP:

```shell
sudo nmcli con mod lan \
  ipv4.method manual \
  ipv4.addresses <address>/<prefix> \
  ipv4.gateway <gateway> \
  ipv4.dns <upstream-dns>
```

Apply:

```shell
sudo nmcli con down lan
sudo nmcli con up lan
```

Verify:

```shell
nmcli con show lan
```

Expected:

```text
ipv4.method: manual
```

---

## Install Samba

```shell
sudo dnf install -y samba samba-dc samba-client krb5-workstation
```

Verify:

```shell
samba -V
```

---

## Disable systemd-resolved

!!! danger

    Samba must own DNS port 53.

```shell
sudo systemctl stop systemd-resolved-varlink.socket
sudo systemctl stop systemd-resolved-monitor.socket
sudo systemctl stop systemd-resolved

sudo systemctl disable systemd-resolved
sudo systemctl disable systemd-resolved-varlink.socket
sudo systemctl disable systemd-resolved-monitor.socket
```

Verify:

```shell
sudo ss -lnptu | grep ':53'
```

No `systemd-resolved` listeners should remain.

---

## Configure Resolver

If `/etc/resolv.conf` points to:

```text
/run/systemd/resolve/stub-resolv.conf
```

replace it:

```shell
sudo rm /etc/resolv.conf
sudo nano /etc/resolv.conf
```

Use upstream DNS for provisioning:

```text
nameserver <router-or-upstream-dns>
```

Validate:

```shell
host google.com
```

---

## Reset Existing Samba Configuration

!!! note

    Required when reprovisioning an existing server.

```shell
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.bak
```

---

## Provision Domain

!!!
    
	When provisioning a new AD, it is recommended by the samba
	wiki to use the NIS extensions by passing the `--use-rfc2307`
	parameter to the `samba-tool domain provision` command.
	[According to the wiki](https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller)
	it has no disadvantages by enabling the NIS extensions.
	But, to add to an existing AD, it requires manual schema
	extension.
	
### Parameter Reference

| Interactive Mode Settting | Non-interactive Mode Parameter | Explanation |
| ------------------------- | ------------------------------ | ----------- |
| `--use-rfc2307` | `--use-rfc2307` | Enables the NIS extensions required for ADUC Unix Attributes tab. |
| `Realm` | `--realm` | Kerberos realm. The uppercase version of the AD DNS domain. For example: `SMITH-LAB.XYZ`. |
| `Domain` | `--domain` | NetBIOS domain (Workgroup). This can be anything, but it must be one word, not longer than 15 characters and not containing a dot. It is recommended to use the first part of the AD DNS domain. For example: `smith-lab`. Do not use the computers short hostname. |
| `Server Role` | `--server-role` | Installs the comain controller `DC` role. |
| `DNS forwarder IP address` | not available | This setting is only available when using the `SAMBA_INTERNAL` DNS backend. For details, see [Setting up a DNS Forwarder.](https://wiki.samba.org/index.php/Samba_Internal_DNS_Back_End#Setting_up_a_DNS_Forwarder). |
| `Administrator password` | `--adminpass` | Sets the domain administrator password. If the password does not match the complexity requirements, the provisioning fails. For details, see [Microsoft TechNet: Passwords must meet complexity requirements](https://technet.microsoft.com/en-us/library/cc786468%28v=ws.10%29.aspx).

Other parameters frequently used with the `samba-tool domain provision` command:

* `--option="interfaces=lo eth0" --option="bind interfaces only=yes"`: If your server has multiple network interfaces, use these options to bind Samba to the specified interfaces. This enables the `samba-tool` command to register the correct LAN IP address in the directory during the join.

```shell
sudo samba-tool domain provision \
  --use-rfc2307 \
  --realm=SMITH-LAB.XYZ \
  --domain=SMITH-LAB \
  --server-role=dc \
  --dns-backend=SAMBA_INTERNAL
```

---

## Configure Kerberos

Review generated configuration:

```shell
sudo cat /var/lib/samba/private/krb5.conf
sudo cat /etc/krb5.conf
```

Backup:

```shell
sudo cp /etc/krb5.conf /etc/krb5.conf.bak
```

Merge Samba settings into:

```text
/etc/krb5.conf
```

---

## Set Administrator Password

```shell
sudo samba-tool user setpassword Administrator
```

---

## Enable Samba

```shell
sudo systemctl disable smb nmb winbind

sudo systemctl enable samba
sudo systemctl start samba
```

Verify:

```shell
systemctl status samba
```

---

## Configure Firewall

```shell
sudo firewall-cmd --permanent --add-service=dns
sudo firewall-cmd --permanent --add-service=kerberos
sudo firewall-cmd --permanent --add-service=ldap
sudo firewall-cmd --permanent --add-service=samba

sudo firewall-cmd --reload
```

Verify:

```shell
sudo firewall-cmd --list-services
```

---

## Validate Active Directory

Domain:

```shell
sudo samba-tool domain level show
```

Zones:

```shell
sudo samba-tool dns zonelist localhost -UAdministrator
```

Expected:

* `smith-lab.xyz`
* `_msdcs.smith-lab.xyz`

Records:

```shell
host -t SRV _ldap._tcp.smith-lab.xyz ::1
host -t SRV _kerberos._tcp.smith-lab.xyz ::1
host dc01.smith-lab.xyz ::1
```

Verify automatic DNS updates:

```shell
sudo samba_dnsupdate --verbose
```

Expected:

```text
No DNS updates needed
```

---

## Configure Reverse DNS

Verify reverse lookup zone:

```shell
sudo samba-tool dns zonelist localhost -UAdministrator
```

Expected:

```text
0.168.192.in-addr.arpa
```

Create reverse zone if missing:

```shell
sudo samba-tool dns zonecreate \
  localhost \
  0.168.192.in-addr.arpa \
  -UAdministrator
```

Create PTR record:

```shell
sudo samba-tool dns add \
  localhost \
  0.168.192.in-addr.arpa \
  10 \
  PTR \
  dc01.smith-lab.xyz \
  -UAdministrator
```

Validate:

```shell
host 192.168.0.10
```

Expected:

```text
10.0.168.192.in-addr.arpa domain name pointer dc01.smith-lab.xyz.
```

---

## DNS Cutover

!!! warning

    Do not continue until all DNS validation succeeds.

Update resolver to use Samba DNS.

```shell
sudo nmcli con mod lan ipv4.dns-search "smith-lab.xyz"
sudo nmcli con mod lan ipv4.dns <dc-ip>
sudo nmcli con up lan
```

Validate:

```shell
host dc01.smith-lab.xyz
host -t SRV _ldap._tcp.smith-lab.xyz
host -t SRV _kerberos._tcp.smith-lab.xyz
host 192.168.0.10
```

All lookups must succeed.

---

## Validate Service Principal Names

Verify LDAP and HOST SPNs were registered correctly.

```shell
sudo samba-tool spn list dc01$
```

Expected:

```text
HOST/dc01.smith-lab.xyz
ldap/dc01.smith-lab.xyz
ldap/DC01
```

If these records are missing, Kerberos authentication and domain joins may fail.

---

## Validate Kerberos

```shell
kinit Administrator
```

```shell
klist
```

Expected:

```text
Default principal: Administrator@SMITH-LAB.XYZ
```

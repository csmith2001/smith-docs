# Joining a Domain

## Prerequisites

* Domain Controller operational
* DNS operational
* Kerberos operational
* Domain user account exists

---

## Configure DNS

!!! danger

    Active Directory relies on DNS.

    The workstation must use a Domain Controller
    as its DNS server before attempting domain
    discovery or domain join operations.

Check current DNS configuration:

```shell
cat /etc/resolv.conf
```

Update NetworkManager:

```shell
sudo nmcli con mod <connection-name> ipv4.ignore-auto-dns yes
sudo nmcli con mod lan ipv4.dns-search "smith-lab.xyz"
sudo nmcli con mod <connection-name> \
  ipv4.dns <dc-ip>
```

Apply:

```shell
sudo nmcli con down <connection-name>
sudo nmcli con up <connection-name>
```

Verify:

```shell
cat /etc/resolv.conf
```

Expected:

```text
nameserver <dc-ip>
```

---

## Verify DNS

```shell
host dc01.smith-lab.xyz

host -t SRV _ldap._tcp.smith-lab.xyz

host -t SRV _kerberos._tcp.smith-lab.xyz
```

Expected:

```text
dc01.smith-lab.xyz has address ...

_ldap._tcp.smith-lab.xyz has SRV record ...

_kerberos._tcp.smith-lab.xyz has SRV record ...
```

!!! danger

    Do not continue until all DNS lookups succeed.

    Domain discovery, Kerberos, authentication,
    and SMB access all depend on these records.

---

## Install Required Packages

### Fedora

```shell
sudo dnf install -y \
  realmd \
  adcli \
  oddjob \
  oddjob-mkhomedir \
  sssd \
  krb5-workstation
```

---

## Verify Domain Discovery

```shell
realm discover smith-lab.xyz
```

Expected:

```text
smith-lab.xyz
  type: kerberos
  realm-name: SMITH-LAB.XYZ
```

---

## Join Domain

```shell
sudo realm join smith-lab.xyz \
  -U csmith-admin
```

Verify:

```shell
realm list
```

Expected:

```text
smith-lab.xyz
```

---

## Verify Kerberos

```shell
kinit csmith
```

```shell
klist
```

Expected:

```text
Default principal: csmith@SMITH-LAB.XYZ
```

---

## Verify User Resolution

```shell
id csmith
```

Expected:

```text
uid=...
gid=...
```

Verify groups:

```shell
id csmith | grep "Storage Users"
```

---

## Enable Home Directory Creation

```shell
sudo authselect select sssd with-mkhomedir --force
```

```shell
sudo systemctl enable --now oddjobd
```

---

## Validate Login

Login as:

```text
csmith
```

or:

```text
csmith@smith-lab.xyz
```

Verify:

```shell
whoami
pwd
```

Expected:

```text
/home/csmith
```

---

## SMB Conf

```shell
sudo nano /etc/sssd/sssd.conf
```

```text
use_fully_qualified_names = False
```

```shell
sudo systemctl restart sssd
sudo sss_cache -E
```

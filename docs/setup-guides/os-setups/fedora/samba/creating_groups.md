# Creating a Samba AD Group

```shell
sudo samba-tool group add group
```

## Add users to group

```shell
sudo samba-tool group addmembers group user
```

## List groups

```shell
sudo samba-tool group list
```

## List members in group

```shell
sudo samba-tool group listmembers group
```

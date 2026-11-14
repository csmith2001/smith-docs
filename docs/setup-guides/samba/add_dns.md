# Creating a DNS Record on a DC

```shell
sudo samba-tool dns add 127.0.0.1 smith-lab.xyz route A IP -U Administrator
```

## View DNS Records

```shell
sudo samba-tool dns query localhost smith-lab.xyz @ ALL -U Administrator
```

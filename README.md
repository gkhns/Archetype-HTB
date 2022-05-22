# Archetype-HTB

**NMAP Scan**

To see the versions of the services running (**-sV**)

To perform a script scan using the default set of scripts (**-sC**)

To scan all ports from 1 through 65535 (**-p-**)

```sh
  nmap -sV -sC -p- 10.129.95.187
  ```

**Open Ports:**

135/tcp   open  msrpc        Microsoft Windows RPC

139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn

445/tcp   open  microsoft-ds Windows Server 2019 Standard 17763 microsoft-ds

1433/tcp  open  ms-sql-s     Microsoft SQL Server 2017 14.0

5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)


47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)

49664/tcp open  msrpc        Microsoft Windows RPC

49665/tcp open  msrpc        Microsoft Windows RPC

49666/tcp open  msrpc        Microsoft Windows RPC

49667/tcp open  msrpc        Microsoft Windows RPC

49668/tcp open  msrpc        Microsoft Windows RPC

49669/tcp open  msrpc        Microsoft Windows RPC





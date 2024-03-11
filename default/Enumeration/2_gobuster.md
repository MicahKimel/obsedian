# gobuster
- directories
```
gobuster dir -u https://example.com -w /wordlists/Discovery/Web-Content/big.txt -t 4 --delay 1s -o results.txt
```
- DNS
```
gobuster dns -d emample.com -t 50 -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt --wildcard
```
- Virtual Hosts
```
gobuster vhost -u http://devvortex.htb -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
```

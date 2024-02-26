# nmap

    nmap 172.217.27.174

# gobuster

    gobuster dir -u https://example.com -w /wordlists/Discovery/Web-Content/big.txt -t 4 --delay 1s -o results.txt

# dirsearch

simple use

    python3 dirsearch.py -u https://target

threading

    python3 dirsearch.py -e php,htm,js,bak,zip,tgz,txt -u https://target -t 20

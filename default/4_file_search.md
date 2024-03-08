
# File Search

- exiftool Authors
```
find . -type f -exec exiftool {} \; | grep Author
```
- awk (get 7th element of each line)
```
awk '{print $7}'
```
- jq
```
echo '[ {"data":[ {"id": 1, "name": "jeff", "active": false}, {"id": 2, "name": "noah", "active": true} ]} ]' | jq '.[].data.[] | (.id|tostring) + ":" + .name + ":" + (.active|tostring) '
```
- see encoding
```
| xxd
```
- change encoding (for powershell)
```
| iconv -t utf16le
```
- base64
```
| base64 -w 0
```
- simple python server
```
python3 -m http.server
```
- spaces
```
echo {hello,hi}
```
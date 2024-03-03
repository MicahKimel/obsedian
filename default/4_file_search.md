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
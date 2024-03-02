- exiftool Authors
```
find . -type f -exec exiftool {} \; | grep Author
```
- awk (get 7th element of each line)
```
awk '{print $7}'
```
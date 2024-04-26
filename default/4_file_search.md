
# File Search

- find / exiftool Authors
```
# find content of file
find . -type f -exec exiftool {} \; | grep Author
# find file name with content
find . -type f -exec grep -l "Justin C. Huffman" {} \;
```
- awk (get 7th element of each line)
```
awk '{print $7}'

# build bash file from wordlists
cat testapis.txt | awk -F: '{print "curl https://salesservice-dev.terminix-triad.com/api/getsalespipeline?SalesEid="$1" > ./dir/url"$1".txt &&"}' > output.txt
```
- jq
```
echo '[ 
{"data":[ {"id": 1, "name": "jeff", "active": false}, 
{"id": 2, "name": "noah", "active": true} ]} ]' | jq '.[].data.[] | (.id|tostring) + ":" + .name + ":" + (.active|tostring) '

jq -n \           
  --arg request_type "InstallApplication" \
  --arg udid "$1" \
  --arg manifest_url "$2" \
  '.udid = $udid
  |.request_type = $request_type
  |.manifest_url = $manifest_url
  '
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
- wordcount
```
wc -l output.txt
```
- head (get first 3 lines)
```
head -n 3 output.txt
```
- vim (end of file)
```
shift g
:$
```
- seq
```
seq 3 | xargs -I {} ./scanCps.sh {}
```
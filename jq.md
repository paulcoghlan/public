# jq

Filter array of objects, selecting by value

```sh
jq '.updatedCases[] | select( .caseId == 38340 )'     
```

Get Flickr Album names
```sh
jq '.albums[] .title' albums.json | tr ' ' '_' | tr -d '[:punct:]' | xargs -I {} mkdir -p "albums/{}"
```
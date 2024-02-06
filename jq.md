# jq

Filter array of objects, selecting by value

```sh
jq '.updatedCases[] | select( .caseId == 38340 )'     
```

Get Flickr Album names

```sh
jq '.albums[] .title' albums.json | tr ' ' '_' | tr -d '[:punct:]' | xargs -I {} mkdir -p "albums/{}"
```

Convert to CSV:
```sh
jq '.[] | [."wp:post_id", ."wp:post_date", .title] | @csv' wordpress.2022-11-27.000.items.json

jq '.[] | [."wp:post_id", ."wp:post_type", ."wp:post_date", .title, ."content:encoded"] | @csv' wordpress.2022-11-27.000.items.json

jq '.[] | select( ."wp:post_type" == "post" ) | [."wp:post_id", ."wp:post_type", ."wp:post_date", .title, ."content:encoded"] | @csv' wordpress.2022-11-27.000.items.json > wordpress.2022-11-27.000.posts.csv

jq -r '.[] | select( ."wp:post_type" == "post" ) | [."wp:post_id", ."wp:post_type", ."wp:post_date", .title, ."content:encoded"] | @csv' wordpress.2022-11-27.000.items.json > wordpress.2022-11-27.000.posts.csv
```

# Oneliner

## Qiitaに投稿したマークダウンを全部ダウンロードする

```
$ curl -Ss -L qiita.com/api/v2/users/onokatio/items?per_page=100 | jq -r .[].url|while read line;do wget $line.md;done
$ curl -Ss -L qiita.com/api/v2/users/onokatio/items?per_page=100 | jq '.[] | [.url,.title]' | xargs -L4 -d '\n' echo|while read -r line;do mv "$(echo "$line"| jq -r .[0] | sed -e 's;.*/items/\([a-z0-9A-Z]*\);\1;')".md "$(echo "$line" | jq  .[1])".md;done
```

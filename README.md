# Oneliner

## Qiitaに投稿したマークダウンを全部ダウンロードする

```
curl -Ss -L qiita.com/api/v2/users/onokatio/items?per_page=100 | jq -r .[].url|while read line;do wget $line.md;done
```

```
curl -Ss -L qiita.com/api/v2/users/onokatio/items?per_page=100 | jq '.[] | [.url,.title]' | xargs -L4 -d '\n' echo|while read -r line;do mv "$(echo "$line"| jq -r .[0] | sed -e 's;.*/items/\([a-z0-9A-Z]*\);\1;')".md "$(echo "$line" | jq  .[1])".md;done
```

## lisponに投稿された音声をダウンロードする

```
curl -Ss https://lispon.moe/lispon/vaqa/listAnswers?aUserId=00000 | jq -r '.data[] | .aTitle, .aVoiceOrigin' | while read line1;read line2;do wget $line2 -O "$line1".flac;done
```

たまに`.aVoiceOrigin`が無いからその場合は`.aVoice`で代用できる

`.aTitle`がなくて`.qContent`でファイル名を代用したい場合は、いい感じに使用不可文字と文字数制限へ対応する

```
curl -Ss https://lispon.moe/lispon/vaqa/listAnswers?aUserId=00000 | jq '.data[] | .qContent, .aVoice' | tr -d '" !()´˘`^_;'| sed -e 's/\\n//g' | while read line1;read line2;do wget $line2 -O $(echo $line1 | cut -c-120).mp3;done
```

## count own diff from git repository

```
ls | while read line;do git -C $line log --log-size 2>/dev/null | grep onokatio -B 1 | grep 'log size' | awk '{a+=$3} END  {print a}';done | awk '{a+=$1} END {print a}'
```

## generate repo xml from github api

```
curl -Ss 'https://api.github.com/users/onokatio/repos?per_page=100&page=1' | jq -r '.[].name' > list
cat list|while read line;do echo "<project path=\"$line\" name=\"$line\" />";done >> default.xml
```

## check status all

```
ls | while read line;do echo $line;git -C $line status -s;done
```

## shindan marker

```
curl 'https://shindanmaker.com/123456' -X POST --data "u=$name" -Ss | pup '.result2 div json{}' | jq -r '.[0] | .text'
```

## convert wav directory to flac

```
find ./hoge/ -name '*\.wav' | while read line;do flac "$line" --compression-level-8 -f -o "$(echo $line | sed -e 's/\.wav$/.flac/')" || sleep 10;done
```

## bulk download emojipacks

```
cat ../*.yaml | yq -c '.emojis[] | [.name, .src]' | while read line;do wget "$(echo $line|jq -rc .[1])" -o "$(echo $line| jq -rc .[0])"."$(echo $line|jq -rc .[1]| sed -e 's/.*\.\(.*\)$/\1/')";done
```

## Twitterでリストに追加済みの人をミュートする

```
twurl --username "onokatio_" "/1.1/lists/ownerships.json?count=1000" | jq -r .lists[].id_str | while read line ;do twurl --username "onokatio_" "/1.1/lists/members.json?count=5000&list_id=$line" | jq -r '.users'  > $line.json;done
```

## container.jsonからtmp??-の名前のついたコンテナを削除する

```
cat containers.json | jq '.identities |= ([.[] | select(.name | test("tmp")==false)] )' | sponge containers.json
```

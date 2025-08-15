# Connpass API

```Shell
curl -X GET "https://connpass.com/api/v2/<Resource>" -H "X-API-Key: <Token>"
```

### CallAPI
```Python
import requests
import json

url = '<uri>'
headers = {
    'X-API-Key': '<Token>'
}
response = requests.get(url, headers=headers)
```

## Resources

### Get Group
```Shell
curl -X GET "https://connpass.com/api/v2/groups/?subdomain=kitasenju-verystrong" -H "X-API-Key: <Token>" -H "Content-Type: application/json"
```

### Response
```Json
{
    "results_returned": 1,
    "results_available": 91,
    "results_start": 1,
    "groups": [
        {
            "id": 1,
            "subdomain": "bpstudy",
            "title": "BPStudy",
            "sub_title": "株式会社ビープラウドが主催するIT勉強会",
            "url": "https://bpstudy.connpass.com/",
            "description": "string",
            "owner_text": "株式会社ビープラウド",
            "image_url": "string",
            "website_url": "http://www.beproud.jp/",
            "website_name": "株式会社ビープラウド",
            "twitter_username": "bpstudy",
            "facebook_url": "https://www.facebook.com/beproud.inc",
            "member_users_count": "5743"
        }
    ]
}
```

### GourpId
```Python
grouId = str(json.load(response["groups"][0])["id"])
```

### Event Details
```Shell
curl -X GET "https://connpass.com/api/v2/events/?event_id=<EventId>" -H "X-API-Key: <Token>"
```

### Response
```Json
{
    "results_returned": 1,
    "results_available": 91,
    "results_start": 1,
    "events": [
        {
            "id": 364,
            "title": "BPStudy#56",
            "catch": "株式会社ビープラウドが主催するWeb系技術討論の会",
            "description": "今回は「Python プロフェッショナル　プログラミング」執筆プロジェクトの継続的ビルドについて、お話しして頂きます。",
            "url": "https://bpstudy.connpass.com/event/364/",
            "image_url": "string",
            "hash_tag": "bpstudy",
            "started_at": "2012-04-17T18:30:00+09:00",
            "ended_at": "2012-04-17T20:30:00+09:00",
            "limit": 80,
            "event_type": "participation",
            "open_status": "open",
            "group": {
                "id": 1,
                "subdomain": "bpstudy",
                "title": "BPStudy",
                "url": "https://bpstudy.connpass.com/"
            },
            "address": "東京都豊島区東池袋3-1-1",
            "place": "BPオフィス (サンシャイン60 45階)",
            "lat": "35.729402000000",
            "lon": "139.718209000000",
            "owner_id": 8,
            "owner_nickname": "haru860",
            "owner_display_name": "佐藤 治夫",
            "accepted": 80,
            "waiting": 15,
            "updated_at": "2012-03-20T12:07:32+09:00"
        }
    ]
}
```

### Values
```Python
eventType = response["events"][0]["event_type"]
eventTitle = response["events"][0]["title"]
eventUrl = response["events"][0]["url"]
# description = response["events"][0]["description"]
eventAddress = response["events"][0]["address"]
eventPlace = response["place"][0]["place"]
```

### Get Event List
```Shell
curl -X GET "https://connpass.com/api/v2/events/?group_id=16046" -H "X-API-Key: <Token>"
```

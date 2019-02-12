## Post suggestions
> https://www.instagram.com/graphql/query/?query_id=17863787143139595&variables={%22first%22:1}

```js
{
  "data": {
    "user": {
      "id": "5373453948",
      "profile_pic_url": "https://scontent-ort2-2.cdninstagram.com/t51.2885-19/s150x150/18949603_433159517065136_526768478904909824_a.jpg",
      "username": "contacting.john.doe",
      "edge_web_discover_media": {
        "page_info": {
          "has_next_page": true,
          "end_cursor": "1"
        },
        "edges": [<list_of_posts>]
      }
    }
  },
  "status": "ok"
}
```

### Strcuture of posts (list_of_posts)
```js
{
            "node": {
              "__typename": "GraphVideo",
              "id": "1662510439487953917",
              "comments_disabled": false,
              "dimensions": {
                "height": 607,
                "width": 1080
              },
              "display_url": "https://scontent-ort2-2.cdninstagram.com/t51.2885-15/s1080x1080/e15/fr/24838868_1994810790786749_3575968905698476032_n.jpg",
              "edge_liked_by": {
                "count": 4187
              },
              "edge_media_to_caption": {
                "edges": [
                  {
                    "node": {
                      "text": "☂️\n- bubbles ☃️I just restocked my shop link in the bio! If you order 3 or more slimes you get a free 2.3oz! Have a nice day 💐 ( comment your favorite emoji"
                    }
                  }
                ]
              },
              "edge_media_to_comment": {
                "count": 42
              },
              "is_video": true,
              "owner": {
                "id": "3995025170"
              },
              "shortcode": "BcSbG-1Dtv9",
              "taken_at_timestamp": 1512406716,
              "thumbnail_src": "https://scontent-ort2-2.cdninstagram.com/t51.2885-15/e15/c236.0.607.607/24838868_1994810790786749_3575968905698476032_n.jpg",
              "thumbnail_resources": [
                {
                  "src": "https://scontent-ort2-2.cdninstagram.com/t51.2885-15/s150x150/e15/c236.0.607.607/24838868_1994810790786749_3575968905698476032_n.jpg",
                  "config_width": 150,
                  "config_height": 150
                },
                {
                  "src": "https://scontent-ort2-2.cdninstagram.com/t51.2885-15/s240x240/e15/c236.0.607.607/24838868_1994810790786749_3575968905698476032_n.jpg",
                  "config_width": 240,
                  "config_height": 240
                },
                {
                  "src": "https://scontent-ort2-2.cdninstagram.com/t51.2885-15/s320x320/e15/c236.0.607.607/24838868_1994810790786749_3575968905698476032_n.jpg",
                  "config_width": 320,
                  "config_height": 320
                },
                {
                  "src": "https://scontent-ort2-2.cdninstagram.com/t51.2885-15/s480x480/e15/c236.0.607.607/24838868_1994810790786749_3575968905698476032_n.jpg",
                  "config_width": 480,
                  "config_height": 480
                },
                {
                  "src": "https://scontent-ort2-2.cdninstagram.com/t51.2885-15/e15/c236.0.607.607/24838868_1994810790786749_3575968905698476032_n.jpg",
                  "config_width": 640,
                  "config_height": 640
                }
              ]
            }
          }
```

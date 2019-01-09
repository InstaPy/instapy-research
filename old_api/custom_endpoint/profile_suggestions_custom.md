## Profile feed suggestions
> https://www.instagram.com/graphql/query/?query_id=17847560125201451&variables={%22fetch_media_count%22:1,%22fetch_suggested_count%22:1,%22filter_followed_friends%22:true}

```js
{
  "data": {
    "user": {
      "connected_fbid": null,
      "edge_facebook_friends": {
        "count": 0
      },
      "edge_suggested_user": {
        "page_info": {
          "has_next_page": true
        },
        "edges": [<list_of_suggestions>]
      }
    }
  },
  "status": "ok"
}
```

### Structure of Suggestions (list_of_suggestions)
```js
{
  "node": {
    "edge_followed_by": {
      "count": 19848599
    },
    "followed_by_viewer": false,
    "full_name": "Laudya Cynthia Bella",
    "id": "2993265",
    "is_private": false,
    "is_verified": true,
    "is_viewer": false,
    "profile_pic_url": "https://scontent-ort2-2.cdninstagram.com/t51.2885-19/s150x150/21569183_1468900316489054_2145823853593493504_a.jpg",
    "requested_by_viewer": false,
    "username": "laudyacynthiabella",
    "edge_owner_to_timeline_media": {
      "edges": [<list_of_user_posts>]
    }
  }
}
```

Structure of user posts (list_of_user_posts)
```js
{
  "node": {
    "shortcode": "BcSARH1FCL2",
    "display_url": "https://scontent-ort2-2.cdninstagram.com/t51.2885-15/e15/24274042_1992511347672316_2141849282267840512_n.jpg",
    "id": "1662392389090943734",
    "thumbnail_src": "https://scontent-ort2-2.cdninstagram.com/t51.2885-15/s640x640/e15/24274042_1992511347672316_2141849282267840512_n.jpg"
  }
}
```

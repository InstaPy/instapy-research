## Post page comments
> https://www.instagram.com/graphql/query/?query_id=17852405266163336&variables={"shortcode":"BcQcdiXltba","first":1}   
Note: You can also use the letters instead of the URLEncoded ones

```js
{
  "data": {
    "shortcode_media": {
      "edge_media_to_comment": {
        "count": 461,
        "page_info": {
          "has_next_page": true,
          "end_cursor": "AQCqN_MXkN63Bsnm1dNm7ClrfpvIdWO_KeZByPf8qHuB_RlqMZSZm46LX2yaoNI27igjadKcdVtK4nG73THDh4PPA4t5jqWUe-df2AytMZqzLw"
        },
        "edges": [<list_of_comments>]
      }
    }
  },
  "status": "ok"
}
```

### Structure of comment (list_of_comments)
```js
{
  "node": {
    "id": "17896560250120231",
    "text": "@su_chainzzz  oh thank you lord Baby Jesus",
    "created_at": 1512417778,
    "owner": {
      "id": "266840572",
      "profile_pic_url": "https://scontent-ort2-2.cdninstagram.com/t51.2885-19/s150x150/18579717_1693158047375888_6891793205646327808_a.jpg",
      "username": "hillshire17"
    }
  }
}
```

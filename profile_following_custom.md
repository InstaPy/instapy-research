## Profile following
> https://www.instagram.com/graphql/query/?query_id=17874545323001329&variables=%7B%22id%22%3A%221948321393%22%2C%22first%22%3A13%7D

```js
{
    "data": {
        "user": {
            "edge_follow": {
                "count": 1091,
                "page_info": {
                    "has_next_page": true,
                    "end_cursor": "AQB0GxMbA6YbSuRrp9FifV6Ph9s7vhwSF9WA02XSctE_uY0WY4DgRVZCgXWCVOeq3XizX5bjmaNpLNX6mNcA6bxXdHOmaVwWKZsnfE7cBFGflQ"
                },
                "edges": [<list_of_following>]
            }
        }
    },
    "status": "ok"
}
```

### Structure of following nodes (<list_of_following> => number depending on the `first` param in the url)
```js
{
    "node": {
        "id": "198454173",
        "username": "zumschwaben",
        "full_name": "Lucas",
        "profile_pic_url": "https://scontent-sjc2-1.cdninstagram.com/t51.2885-19/s150x150/12747617_730137827087703_1179248522_a.jpg",
        "is_verified": false,
        "followed_by_viewer": false,
        "requested_by_viewer": false
    }
}
```

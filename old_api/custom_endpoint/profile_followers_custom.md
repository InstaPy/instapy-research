## Profile followers
> https://www.instagram.com/graphql/query/?query_id=17851374694183129&variables=%7B%22id%22%3A%221948321393%22%2C%22first%22%3A1%7D

```js
{
    "data": {
        "user": {
            "edge_followed_by": {
                "count": 4471,
                "page_info": {
                    "has_next_page": true,
                    "end_cursor": "AQC0f_x72nC4qc7wV-ihGLu_p1vHnFBevbySgkJ6HXsevQG9ECO1V27ekOcCLqGbkRtEEjN__kPlVH5Qwftkl1u79GDG_ox40CD2pLAKJV_3Mg"
                },
                "edges": [<list_of_followers>]
            }
        }
    },
    "status": "ok"
}
```

### Structure of follower nodes (<list_of_follower> => number depending on the first param in the url)
```js
{
    "node": {
        "id": "574768113",
        "username": "bauerntuete",
        "full_name": "Bauerntüte",
        "profile_pic_url": "https://scontent-sjc2-1.cdninstagram.com/t51.2885-19/s150x150/12357309_1675312869382759_451960668_a.jpg",
        "is_verified": false,
        "followed_by_viewer": false,
        "requested_by_viewer": false
    }
}
```

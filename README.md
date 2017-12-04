# InstaPy-Research
📄 Research repository for InstaPy

> This repository documents the, up to this point, found endpoints that could be useful for InstaPy to improve it's fault tolerance and performance.    
The goal is to not only remove the unnecessary use of selenium to find elements to click but also improve the amount of data that can be retrieved in each run. Once we reach a point where a profile/post only has to be opened to actually like, comment  and follow the profile, we minimized the amount of elements that need an upgrade on an Instagram webpage change.    
Note: This is just an assumption, but the GraphQL endpoints most likely are the same for the mobile webpage. This means that once it's chagned in the code, we can easily switch to the mobile version which allows us to build a post scheduler into InstaPy. 

## Basic Information that are displayed when accessing a page

**Schema:**
```bash
https://instagram.com/<path>?__a=1
```

**[Examples](./basic_endpoint)**
```bash
# Explore tags
https://www.instagram.com/explore/tags/vegan/?__a=1

# When logged in, feed
https://www.instagram.com/?__a=1

# Own/other profiles
https://www.instagram.com/contacting.john.doe/?__a=1
```

## GraphQL modifiable data endpoints

**Schema:**
```bash
https://www.instagram.com/graphql/query/?query_id=<query_id>&variables=%7B<parameters>%7D
```

| QueryID | Endpoint |
|---------|----------|
|17875800862117404|posts for tags|
|17874545323001329|user following|
|17851374694183129|user followers|
|17888483320059182|user posts|

Available parameters:
- id (user_id)
- tag_name (only one needed for explore tags)
- first (amount of nodes to get)
- after (haven't completely figured out, but looks like this is for the offset, use the cursor field)


**[Examples](./custom_endpoint)**
```bash
# Explore tags
https://www.instagram.com/graphql/query/?query_id=17875800862117404&variables=%7B%22tag_name%22%3A%22<tag_name>%22%2C%22first%22%3A<num_of_posts>%7D

# User profile following
https://www.instagram.com/graphql/query/?query_id=17874545323001329&variables=%7B%22id%22%3A%221948321393%22%2C%22first%22%3A<num_of_following>%7D

# User profile followers
https://www.instagram.com/graphql/query/?query_id=17874545323001329&variables=%7B%22id%22%3A%22<user_id>%22%2C%22first%22%3A<num_of_followers>%7D

# User profile posts
https://www.instagram.com/graphql/query/?query_id=17888483320059182&variables=%7B%22id%22%3A%22<user_id>%22%2C%22first%22%3A<num_of_posts>%7D
```


> Note: This uses URLEncoded for the variables

|Sybmol|URLEncoded|
|------|----------|
|,|%2C|
|}|%7D|
|{|%7B|
|:|%3A|
|"|%22|

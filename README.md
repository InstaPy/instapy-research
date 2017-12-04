# InstaPy-Research
📄 Research repository for InstaPy

### Basic Information that are displayed when accessing a page

**[Schema:]()**
```bash
https://instagram.com/<path>?__a=1
```

E.g.
```bash
# Explore tags
https://www.instagram.com/explore/tags/vegan/?__a=1

# When logged in, feed
https://www.instagram.com/?__a=1

# Own/other profiles
https://www.instagram.com/contacting.john.doe/?__a=1
```

### GraphQL modifiable data endpoints

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


E.g.
```bash
# Explore tags
https://www.instagram.com/graphql/query/?query_id=17875800862117404&variables=%7B%22tag_name%22%3A%22<tag_name>%22%2C%22first%22%3A<num_of_posts>%7D

# User profile following
https://www.instagram.com/graphql/query/?query_id=17845312237175864&variables=%7B%22id%22%3A%22<user_id>%22%2C%22first%22%3A<num_of_followings>%7D

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

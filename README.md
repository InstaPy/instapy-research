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

```bash
# Explore tags
https://www.instagram.com/graphql/query/?query_id=17875800862117404&variables=%7B%22tag_name%22%3A%22<tag_name>%22%2C%22first%22%3A<num_of_posts>%7D
```

> Note: This sses URLEncoded for the variables

|Sybmol|URLEncoded|
|------|----------|
|,|%2C|
|}|%7D|
|{|%7B|
|:|%3A|
|"|%22|

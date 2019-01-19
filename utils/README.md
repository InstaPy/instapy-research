# Utilities

# Table of Contents

- [Webdriver PyPi package](#webdriver-package)

# Webdriver package

- PyPi package
- updates itself via travis
	- searches for any webdriver udpate
- small size -> no binaries in package
- downloads used driver on startup
- is able to download specific versions

## requirements

- a PyPi installed package is able to ...
	- [  ] download files
	- [  ] extract zip archives
	- [  ] chmod file with permission 0755
	- [  ] execute self permissioned binary

## preferred example usage

```python
from webdriver import chrome
from selenium import webdriver

driver = webdriver.Chrome(executable_path=chrome.binary('2.45'))
driver.get("http://www.python.org")
assert "Python" in driver.title
```

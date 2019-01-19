# GUI Roadmap

# Modules

This whole project will be based on the following independent components:

- [Python webserver](#webserver)
- [JavaScript based GUI](#gui)
- [Auto deploy platform](#deploy-platform)
- [ELK Stack](#elk)

There also have to be changes in the main instapy package:

- [core changes](#core-changes)

# Main concept

Everything should be really abstract and dynamic. All functionality and available functions of instapy core will only be configured in **one** file.  
_Example:_  
Neither the Webserver nor the GUI knows about the function `follow_users_follower()`. They also dont know anything about the signature of this function.  
There will be one json or yaml configuration file which will looks like the following: _(alot of properties might be missing here, just to get an idea)_  
```json
{
	"methods": [
		{
			"name": "follow_users_follower",
			"params": [
				{
					"name": "hashtags",
					"positon": 0,
					"type": "list",
					"optional": false
				},
				{
					"name": "randonum",
					"positon": 1,
					"type": "int",
					"optional": true,
					"default": 0
				}
			]
		}
	]
}
```

If there is another 'job' or 'feature' added to instapy core later on, only this file has to get updated.  
Webserver and GUI will read the new file, and automaticly configure themself for the new function.  

**Idea:**  
- are you able to get all of the data for the config out of instapy core with the `dir()` function ?
- maybe get some additional stuff with help of [functools](https://docs.python.org/2/library/functools.html) Python library?

-> Developers of instapy-core just have to provide a pydoc string which got a certain format and the GUI / Webserver are going to configure based on those
-> even the config file would be obsolote
-> People could switch instapy versions and the same GUI / Webserver will work on every instapy version because it configures itself based on the package

**-> [have a look at my tests !!](/gui_roadmap/inspecting_package.ipynb)**

# Webserver

_This will be a GraphQL wrapper for the instapy package. (maybe ?)_  

- save and load current configuration
- save configuration templates _(like different quickstart files)_

**Idea 1 -> go full dynamic:**

Not a single function will get it's own endpoint or url.  
Based on Main concept.

- GraphQL would be bad for this method
	- or maybe just one really well designed resolver which handles everything ?
	- plain json is possible in GraphQL
	
- [GraphQL for Python](https://graphene-python.org)

# GUI

- Electron
- Preact ? 

Each commit on master triggers 2 different builds:  

1. Bundle instapy, webserver, GUI with electron into a desktop application
	- good for all 1 click start people
1. Bundle GUI into a SAP which will be served by the webserver
	- good for running instapy on a server
	- solution we gonna deploy on the deploy platform for money

- Users got the same GUI everywhere
- Platform independent
- even same GUI if instapy runs on a server

## Styling

CSS-Frameworks: _(1 and 2 are my fav)_
1. [Sematic UI](https://semantic-ui.com)
1. [Foundation](https://foundation.zurb.com)
1. [Bootstrap](https://getbootstrap.com)
1. [Milligram](https://milligram.io/#forms)
1. [Materialize](https://materializecss.com/collections.html)
1. [Bulma](https://bulma.io)
1. [Spectre](https://picturepan2.github.io/spectre/)

# Deploy platform

- maybe not open source ?
	- would be the only component which would be private
- [Auth0](https://auth0.com) as authentication
- each user gets a single docker instance
	- is there a more lightweight solution ?
	- is serverless even possible in our case ?
- deploy on digital ocean ?
	- rent one big server or alot of small droplets ?
- research for other cheap and scaling hosting solutions

## Pricing

- 5 - 10 Euro per month for server hosting
- 30 Euro per month PREMIUM
	- We gather information about which functions and methods will give you the most followers
	- based on data from ELK stack
	- We collect the data from all servers we are running on the platform
	- User gets most optimized bot configurations based on analysis

- pay as you go model
	- only pay when your bot is running
	- buy on the fly
	- buy x hours like prepaid

- min. 50% of income will be put into open collective for instapy, gui and webserver

# Core changes

- sending all logs to ELK stack
- send data analytics to ELK stack
	- follower grow
	- exceptions and errors
		- let's us fix issues before they appear on github
	- logs

- ELK stack support can be disabled
	- is enabled by default

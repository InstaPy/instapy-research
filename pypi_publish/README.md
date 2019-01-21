This repository documents the process of taking InstaPy and publishing it to PyPi. It also documents the steps we needed to take in order to get the tool from a high demand setup to a simple `pip install`.
One of the most important concepts we want to quickly cover here is how to publish wheel/multi distributions for different platforms.

## Basic publishing Process for PyPi
> TODO

## Publishing wheel/mutli distributions
> TODO


--- 
---

# Technical Documentation of PyPi deploy by @uluQulu

You can read any or all of it or not.  
It's just written also for me to reference back when I forget about some terms.  
And for you to understand new terms, used tools and why I chose them.

> This text <i>is intended for</i>/<i>relies on</i> my PR at https://github.com/timgrossmann/InstaPy/pull/3722.

## Deploy InstaPy to PyPI

You have a great code and you want to **distribute** it for others to use.  
Options are:  

- send it by email;  
- send a copy of it using local storage;  
- tell them to download it from an online storage (_your website or e.g. Github page_);  
- tell them to download it from PyPI 😋  
 Benefits:  
- - everyone knows PyPI;  
- - easily manage it, e.g., install/update/uninstall with `pip`;  
- - PyPI is for free and will live longer than many other services;  
- - easy to track with PEP compliant versions;  
- - everyone will be able to use your code _as you release_ regardless of their _platforms_;

--- 

### uploading a package to PyPI [by hand]

- first you have to have a valid **setup.py** file to make a distribution
- you have to make a distribution to upload it to PyPI
- there several types of distributions, such as
- - source distribution format is called `sdist`  
Makes `instapy-0.0.3.tar.gz` file at **dist** folder and is a source archive;

- - binary distribution format (wheel): `bdist_wheel`  
Literally stands for "_build me a wheel binary distribution file_"  
Makes `instapy-0.0.3-py2.py3-none-any.whl` file at **dist** folder and is a built distribution;

Newer pip versions preferentially install built distributions, but will fall back to source archives if needed;  
You should always upload a source archive and provide built archives for the platforms your project is compatible with;  
In our case, **instapy** package is compatible with Python on any platform so only one built distribution is needed;

- - some of the other formats ~nobody use (_eh..._):  
`bdist`: create a built (binary) distribution  
`bdist_wininst`: create an executable installer for MS Windows  
`bdist_rpm`: create an RPM distribution  
`bdist_egg`: create an "egg" distribution  

>**Hint**:
Running `python setup.py  --help-commands` in your console would list them and more..

<sub>
    <i><b>Note</b> that</i>, you may have as many built distribution files for your package as you like to support different python implementations, platforms, architectures but you can have only 1 source distribution for your package.
</sub>

#### Okay why we need built distributions?
- When your package does not have a built distribution and only have source distribution;  
 `pip` will try to build that package from the source distribution which takes some time.  
 And when it is a big project, such as a scientific project where build could take a lot of time..  
 So that built distributions come along!  
 Once a built distribution provided in a package [release], then pip will install it in a second without building it again.

- Also another point is when your package incorporates other programming langauges such as C,  
 and while installing that package from a source distribution requries C build tools and in case of your user does not have those tools, then he/she cannot build that package and install it.

Our code in **instapy** does not actually have C extensions. Then there's nothing to 'build'. But we will still provide a wheel cos it'll install a little bit faster 😋

- Also, note that, some packages having code from external languages that require specific compilers and users might not have those compilers and then they cannot build your package to install it.  
 For that reasons, some packages like **numpy** (_scientific library requiring non-usual compilers_) provides tons of wheel built distributions for people to install it without building and also provide a source distribution for everyone.

#### What is a `wheel` please 😥 ?
- It's the name came from a wheel of cheese.  
 You put wheels in a Cheeseshop.  
- `wheel` is the file format that is currently the standard for **pip**.  
- To provide `wheel`s for lots of platforms, you can use **cibuildwheel** which is a handy package for doing that.  

---

### Python packaging
  
- [The Python Packaging Guide](https://packaging.python.org) - very helpful and recommended source to get first aid help.
- [Sample project](https://github.com/pypa/sampleproject) - a skeleton project for a python package.
- General case & maintenance required, @timgrossmann.
- _distutils_ [old] vs _setuptools_ [current] vs ...
- **setup.py** (_currently requirement_) or **setup.cfg** or **pyproject.toml** or ...
 
#### How to make a distribution?
Firstly, we should probably go with **setup.py** for now.  

- It has more reference and honestly we might jump straight over **setup.cfg** into **pyproject.toml** or **Pipfile** as the next '_standard_'.  
- Also, knowing that, **setup.cfg** is a replacement for **setup.py** when using _setuptools_. **pyproject.toml** allows replacing _setuptools_ with other things whereas **Pipfile** is for _pipenv_ only.

Lastly, we should really go with **setup.cfg** or **setup.py** and **Pipfile** or **pyproject.toml** is not recommended at the moment.

##### What I have done?
- I've used **setup.py** for everything it could.
- Also benefited from **setup.cfg** in cases **setup.py** couldn't do.  
- - Such as, adding,  
 `universal = 1`  
 to the [bdist_wheel] metadata field, will produce us  
 **instapy-0.0.3-py2.py3-none-any.whl**  
 built distribution file _rather_ than,  
 **instapy-0.0.3-py3-none-any.whl**  
 It's very good cos **instapy** is _pure python_ and the _distribution_ should also be like so.

- - Or being able to set _flake8_ configuration right in it,  
```python
[flake8]
max-line-length = 79
exclude = .tox
```
is awesome.


#### bundling package data files
+ **MANIFEST.in** - used for source distributions
+ `package_data` parameter inside `setup()` function - used for both source and binary distributions
+ `include_package_data=True` - packs every data file in the package (`packages=["instapy"]`)  
 In our case if it was used, it was gonna bundle the zipped original icons which is not being used [internally].  
 So, I manually set required data files (_just icon files_) inside `package_data` parameter.

#### getting package data files after installation
I've used the **pkg_resources** module to achieve it.  

+ The **pkg_resources** module distributed with **setuptools** provides an API for Python libraries to access their resource files.  
+ You can see its usage inside the **quota_supervisor.py** file where I used it to get the icons from the `"instapy"` package.  
+ It works if the package is local (_e.g. a git repository_) or is installed from **pip**.  
_Apparently, once you are in a local repository, it has a higher priority than the **pip** install location_.  
+ It provides a non-filesystem API to access your data.  
_Consider things like **pyinstaller** where maybe your data won't end up on a filesystem at all_..  

---

### Okay, I will publish it.

Assuming you want to make a source distribution and wheel binary distribution both,
```python
python setup.py sdist bdist_wheel
```
running that command will make a new folder called **dist** (_if not exists_) having the distribution files you have just made in it.  
**instapy-0.0.3.tar.gz**  
**instapy-0.0.3-py2.py3-none-any.whl**  
so, navigate to that folder and make sure those files exist in there.

Now you want **twine** package to publish your files to PyPI.  
Why **twine** rather than .. [old upload method]?  

- **twine** has always been secure from the start  
- **setuptools** can automatically upload your files but you shouldn't use that command for various reasons one of which is being insecure  
- it can _check_ the distribution files for you [such as, before uploading]  
- etc.

So,
```python
pip install twine
```

Now, check the files in the dist folder with **twine**
```python
twine check dist/*
```

If they pass successfully, you can upload to PyPI now 🎊
```python
twine upload dist/*
```
or if that fails [for some reason..]
```yaml
twine upload --repository-url https://pypi.org/legacy/ dist/*
```

or you have many [new] distribution files in the **dist** folder and wanna upload only specific one
```python
twine upload dist/instapy-0.0.3.tar.gz
```

if you want to upload to TestPyPI,
```yaml
twine upload --repository-url https://test.pypi.org/legacy/ dist/*
```

Note that, for both [real] PyPI and TestPyPI, if the package does not exist in the name you have provided, it will automatically take that name as you upload.  
I mean, you do not need to _register_ your package [name] before uploading anymore.

Also, in both methods using **twine**, you will have to provide your username and password on the repository URLs [either PyPI or TestPyPI].  
  
Yeah, you tried upload but it failed with a less explanatory error message 😒
try this,
```yaml
twine upload --verbose dist/*
```
adding `--verbose` flag will output **all** of the messages and you can easily find out the problem with the upload.



#### pip
What is **pip**?  

- it initially was called **pyinstall**   
- prior **pip**, there were **easy_install** which was e.g. unable to _update_, _uninstall_ packages..  
 **easy_install** introduced a new type of built distribution - the **egg** distribution;  
- PIP stands for "**P**ip **I**nstalls **P**ackages"  
- PIP definitely ignores **eggs**


#### PyPI
- PyPI was also called _"The Cheeseshop"_ 🧀 (_from a joke_), cos when PyPI was introduced there were ~no package inside it 😂
- Now is also called _Warehouse_. _Warehouse_ is a place to put packages (`https` everywhere, modern web framework and _TestPyPI_) and officially became _PyPI_ in 2018 April. 
- **pip** installs the latest version of the package from PyPI unless specified
- you can upload a filename (_e.g. a source distribution file named - `instapy-0.0.3.tar.gz`_) to PyPI or TestPyPI **only one time** (_even deleting the package won't help_ 👩🏽‍🚀)
 

#### Automated versioning 
- **setuptools_scm** [most preferred]
- **versioneer** [less annoying]

I have chosen not to use _automated versioning_ using such tools which are a bit restriction oriented.  
For a while, this project should use _manual versioning_ by changing the version number at **__init__.py** file,  
`__version__ = "0.1.1"`  
update it regularly to make new releases to PyPI.

## Workspace folder introduced
   
The biggest importance of having workspace folders is,  
now **instapy** is fully a standalone library and does not depend of any external files.

Instead, now it has user's data files which are located in default (_user's home folder_) or custom location user sets.

That was the big step to be made and it was done successfully.  
Read about the workspace folder and its usage in the PR description of https://github.com/timgrossmann/InstaPy/pull/3722.

## Universal Testing Framework- tox with pytest & flake8

### tox
    
To sdist-package, install and test your project, you can now type at the command prompt:
```yaml
tox
```
This will sdist-package the current project, create required virtualenv environments [per configuration at tox.ini], install the sdist-package into the environments and run the specified command(s) in each of them.

Or with:
```yaml
tox -e py36
```
you can restrict the test run to the python 3.6 environment.

Or run a more sophisticated _single_ python test,
```yaml
tox -e py27 -- InstaPy/tests/test_settings.py:test_settings
```

All of them can be done **locally** before pushing to the external server(s) which is good, thanks to **tox**.  
And e.g. when pushed to Github, Travis CI will run the **exact same** procedure in its machines automatically (_if the build job condition(s) is/are fulfilled_).

#### tox.ini configuration (_highlights_)
`envlist = py27, py34, py35, py36, py37, flake8-27, flake8-37`  
- Are the environments for the code to be tested (_by **pytest**_), analyzed (_by **flake8**_) on.

`skip_missing_interpreters = true`  
- Basically, **tox** makes virtual environments based on your real python installations.  
As in **tox** _config_ environments are,  
`envlist = py27, py34, py35, py36, py37, flake8-27, flake8-37`  
and you have no e.g. python 3.4 (_**py34**_) installed on your machine,  
there, `skip_missing_interpreters` says, skip this environment and test on all others available.

`minversion = 2.5.0`  
- Minimum version of **tox** must be installed on the tester's system to be able to run **tox**.


`requires = tox-venv`  
- It's required by new python 3s for **tox**..

```yaml
commands
    = python -m pytest {posargs}
```
- That command is quite interesting;  
cos you could just do,
```yaml
commands
    = pytest {posargs}
```
but then in some python implementations, it would fails to get the `PYTHONPATH`  
and then you had to add an extra parameter,
```yaml
setenv = 
    PYTHONPATH = {toxinidir}
```
to the **testenv** metadata

- What `python -m` does extra is, it also sets your `PYTHONPATH` before executing **pytest** command

- What does `pytest {posargs}` do 🤒  
it accepts **pytest** commands while running from the command line, e.g.,
```yaml
tox -- -x
```
**tox** doesn't interpret arguments after `--` and it just passes them as `{posargs}`  
so, in our case, `-x` belongs to **pytest**,  
and in particular, it means - "_stop after first failure_".

---

### pytest
    
**pytest** is the actual tool that tests the code since **tox** simply just runs it on several different environments it makes.

For **pytest** to discover tests, you must have a valid naming convention.  
There are a few rules for test discovery in **pytest**.  
Some of them are,  
  
- **pytest** looks for files named **test_*.py** or ***_test.py**.  
-  classes must start with `Test`, and can't have an `__init__` statement.  
-  etc.

Using **setup.cfg** file,
```python
[tool:pytest]
testpaths = tests
```
I've set the location of test files which is the **tests** folder at project root folder.

There were several test files in our **tests** folder written by a good tester.  
But they were written a year ago and today they are totally outdated.  
So I added them into an archive [modification locked rar file] and not deleted them.  
Cos some guy good at writing tests can easily fix those outdated blocks and re-enable those strong tests and even himself/herself write new tests there by looking at it.. 

And yet, I added a simplest valid test example to there for now- **test_settings.py**.

--- 

### flake8
    
I have added **flake8** tests for both python 2.7 and python 3.7.  
But if you ask what we needed - **flake8** for python 2.7 is sufficient.
```yaml
commands =
    flake8 . --count --select=E722,F401,F811,E901, \
           E999,F821,F822,F823 --show-source --statistics
    - flake8 . --count --max-complexity=10 \
             --max-line-length=79 --statistics
```
it's the exact **flake8** check we previously had inside the **.travis.yml** configuration file.  
Only difference is,  
`- flake8` 👈🏼  
previously in **.travis.yml**, it had `--exit-zero` that meant - "_return 0 even if the check fails_"  
but since we used **tox**, things are easier,  
`- flake8`  
a dash before a command means return 0 afterall whatever this command returns..  
So, I could just use the **flake8** way (_without dash and with **exit-zero**_) too, but I chose **tox** way cos it's **tox**'s playground xD  
  
`skip_install = True`  
It skips installing **flake8** inside the virtual environment that we don't need to.

##### Bonus
Using **setup.cfg**, we can set some **flake8** options for tools like **tox** to grab
```yaml
[flake8]
max-line-length = 79
exclude = .tox
```

`exclude .tox` means, do not check the files inside the **.tox** directory while running **flake8** checks..  

>**Hint**:
Inside **.tox** directory, resides the virtual environments made by **tox** for _analysis_, _testing_, etc.

## Upgrade Travis CI usage (tox as build script)
   
### Travis CI
    
First thing comes to mind;  
Why I have changed the default build script to use **tox** as the build script?   
There are lots of points for that I have no intention to explain.  
But I will tell a few points.

Travis has so inconsistent, poorly documented (_still no any available reference on all of the parameters available_) with a very frequently changing API and even conflicts in documentations.  
And there is Travis CI dot org and Travis CI dot com which work crazily separated with one another and documentation is affected by that, too.

**tox** is stable, universal and father of multi-platform testing with a super clear documentation and with web full of examples.

Yeah, as millions, we are thankful to Travis CI for offering free usage on open-source projects.  
E.g. Circle CI allows 1000 builds per month for open-source projects..  
Jenkins does not have a Github Integration at all and is written in Java 🤢  
BTW Travis CI is written in Ruby 💎

Travis CI has linux/mac/windows which is awesome. But they don't give you any pythons outside of linux.  
They don't offer you a cache you so you have to rebuild/install them for every job.  
And when you build a wheel you literally have to offload it somewhere else just to be able to ever try it.  
Travis CI does not have capability for auto-deployment on PR merge and can auto-deploy on tags which we will be using.

CircleCI is better in all those regards above 👆🏼  
They actually hold artifacts and has caches unlike Travis CI.  
They allow auto-deployment on PR merge or any other condition you like.  
One more thing I personally like about CircleCI is you explicitly set upload command e.g. to use **twine**.

#### Why I have used Travis CI
Mostly cos of we already were using it finely for quite some time and got used to it 😊  
Also, core team agreed on auto-deployment on new git tags, also not having artifacts stored is not a problem for a pure python project like **InstaPy**.  
Also, Travis CI is free without boundaries [for open-source projects] that is so good.  
E.g. CircleCI can decrease their 1000 build limit one day but Travis CI will unlikely do it..

---

### tox & .travis.yml
   
Alright, we have the power of **tox** inside Travis CI;  
What is cool that, it's **tox**,  
if you run **tox** in your local machine(s) or Travis CI runs it on its machine(s),  
it will be the same!  
So, for maintainers, running **tox** on their machines before pushing to upstream will help them fix the bugs before their PR fails by Travis CI in the remote-end..

Earlier, it was not possible, if possible- would take extra customized tweaks, etc.  
But now, it is done in a harmonious order from one hand, by **tox**.

#### .travis.yml explained
Cos of Travis CI documentation is a bit troublesome, I will explain everything I have used inside the **.travis.yml** file ~line-by-line :]

```yaml
dist: xenial
```
dist means linux's distro in use  
`"xenial"` is the newest version of linux in Travis servers which is Ubuntu 16.* AFAIR  
e.g. `dist: trust` is old version of Ubuntu which is 14.* AFAIR  

So, today `xenial` should be the preferred distro for ~normal usage.


```yaml
sudo: false
```
We don't need to run as _sudo_ within our current configuration.

```yaml
python: "3.6"
```
Fine, what now;  
We have used tox to set custom python environments but now setting python here again?  
- Well, that sets the default python for the system.  
- That default python will be used when no specific-python is specified.  
- Which job has python unspecified? - _auto-depyloyment_ job; and during that job, it will use that default python we set- version `3.6`.

```yaml
cache: pip
```

Caches pip (_e.g. installed packages,.._) 😋 to speed up the further build processes.

```yaml
branches:
  except:
    - legacy
    - experimental

```
It means build (_entirely- static analysis, tests, deployment.._) only if the change comes from a branch that is neither `"legacy"` nor `"experimental"`.  
Well, our current scheme has not those guidelines [yet].  
But I thought of adding that configuration to be used in future which would be very useful.


```yaml
install: pip install tox tox-venv
```
Well, with Travis CI, the typical build process has the following steps:  
**1**. `before_install`  
**2**. `install`  
**3**. `before_script`  
**4**. `script`  
**5**. `after_success`/`after_failure`  
**6**. `after_script`

If specified, these take place after the `after_script` has run.  
**1**. `before_deploy`  
**2**. `deploy`  
**3**. `after_deploy`

As you can see, `install` step is being run at the very early step.

So that, to be able to use **tox** as our build script,  
we have to first install it in the `install` step.  
**tox-venv** is also requried these days with newer pythons.

```yaml
script: tox
```
It is tox which will take after the build process rather than very customized, generally-non_understandable, implicit shell scripts or basic non-genuine defaults.

```yaml
stages:
    - name: static analysis
    - name: test
    - name: deploy to PyPI
      if: type = push AND fork = false AND tag =~ ^\d+\.\d+\.\d+
```
Here we have got 3 stages named as above.  
The third stage named `"deploy to PyPI"` has conditions to be able to be triggered.  
Those conditions are  
- the type of the change must be `push` (_e.g. from `git push `_)  
- the change must come from non-fork repository (_I think it is now the default of Travis CI but to be sure, I explicitly added it_)  
- the change must have a tag with it (_e.g. from `git push --tags`_) and the tag must match the given regular expression above

When those conditions are not satisfied, then it will not trigger that stage.

```yaml
jobs:
  fast_finish: true
  allow_failures:
    - python: "2.7.6"
  include:
    - stage: static analysis
      env: TOXENV=flake8-27
      python: "2.7"
```
**jobs**, yeah, it all starts here.  
Travis CI runs the jobs included here in the given order they are listed.
```yaml
fast_finish: true
```
It means, if we have `allow_failures` enabled for some python version, and then that python version fails the test. Then, it will allow other jobs in the next stages to also run.

Note that, normally, if a job fails in a stage, then the next stage or stages will not run and the build will fail (_stop_).  
But also, if a job fails in a stage, then all other jobs will run normally in that same stage, normally.

So, okay. I have included python `2.7.6` which is being run in test stage to be allowed to fail.  
And if that job fails, then other stages will still run and build will go normally and if everything else goes fine, build will succeed.  
The difference will be, when you visit Travis CI, you will see that the _test_ on that oldie python has failed.


```yaml
    - stage: test
      env: TOXENV=py27
      python: "2.7"
```
I've talked a bit about stages above,  
and well, this is another job at test stage.  
It will make a new tox virtual environment of python 2.7 in a Travis CI python 2.7 implementation as defined there and then.  
The rest is tox, it runs what you have defined for the python 2.7 environment in your **tox.ini** configuration (_in our case generic `testenv`_).


```yaml
    - stage: test
      env: TOXENV=py27
      dist: trusty
      python: "2.7.6"
```
Chose `dist: trusty` (_default- `xenial` is used when not-specified_) for Travis CI to install the old python on an older (_but more stable_) linux distro (_trusty is Ubuntu 14.* I think_) for obvious reasons..

```yaml
    # - stage: test   # <-- this jobs fails to install googleapis-common-protos
    #   env: TOXENV=py35
    #   python: "3.5.2"
```
Yes, I couldn't fix it elegantly.  
**googleapis-common-protos** has a bug and fails to be installed with pip as a dependency;  
_google_ guys blame **setuptools** for that, idk.. It occurs only on python 3.5 with **setuptools** > ~28.8.0 (_it's a namespace conflict_) ...

So, having not so important, I have turned off test stage on python 3.5.  
BTW, **googleapis-common-protos** is being used only by **clarifai** for **InstaPy**.


```yaml
    - stage: deploy to PyPI
      install: true
      script: skip
      after_success: true
      after_failure: true
      deploy:
        provider: pypi
        user: InstaPy
        password:
          secure: l_ongCod_e
        distributions: sdist bdist_wheel
        on:
          tags: true
          repo: timgrossmann/InstaPy
          branches:
            only:
              - master
```

```yaml
install: true
```
It is one another weird usage off Travis CI.  
It means, during this job at this stage, instead of installing **tox** & **tox-env** from pip (which is an `install` default), just do nothing and return `true`.

```yaml
script: skip
```
It skips that part, cos inside deployment job, we do not need **tox** to be run.

```yaml
          secure: l_ongCod_e
```
Some guys use old methods passing password as an environmental variable which I find a bit insecure [in the long-run] and recommend encrypting passwords **locally** (_important_) using Travis's _Ruby_ **gem** package- [**travis**](https://github.com/travis-ci/travis.rb) which also includes your personal key during encryption to make it uncrackable 🤫

```yaml
          tags: true
```
Deploy only if the change has a tag.  
E.g. normal pushes- `git push` [without a tag] will not trigger that deployment stage as expected.

```yaml
          branches:
            only:
              - master
```
Deploy only if the changed branch is `master` and nothing else.

```yaml
after_success:
  - wget https://raw.githubusercontent.com/DiscordHooks/travis-ci-discord-webhook/master/send.sh
  - chmod +x send.sh
  - ./send.sh success $DISCORD_WEBHOOK
```
Inside `after_success` & `after_failure`,  
it will send a message to our Discord channel.  
Read the section with "_Discord webhook_" which explains the details of it.

```yaml
    recipients:
      - contact.timgrossmann@gmail.com
      - shahriyarguliyev@hotmail.com
```
It allows to include multiple recipients to receive emails on final build status right from its configuration file.

```yaml
    on_success: change
    on_failure: always
```
`"change"` means notify me only when the build status changes from Failing (_earlier build_) to Passing (_current build_) or vice verse.  
`"always"` means notify me always regardless of the build status.

---

### Encrypting PyPI password for Travis CI
    
You have to provide your PyPI password [alongside your username] inside your Travis CI configuration file - **.travis.yml** for Travis CI to be able to auto-deploy your package to PyPI.

Travis CI service offers a Ruby tool called [**travis**](https://github.com/travis-ci/travis.rb) can be installed easily using **gem** - Ruby's package manager.  
It will encrypt your password locally through command prompt.

Here are the **steps** _required_* to get it done:  

**1**-) Make sure you have Ruby programming language installed in your system.  

**2**-) Install **travis** package using **gem**.  

**3**-) Navigate to the project root directory.  

**4**-) Run 
```yaml
travis login
```
in the command prompt.  
And there, enter your Github _username_ and _password_.  
IMO, Travis CI uses that information as a personal key while encrypting your PyPI password.  
If you don't want to enter your Github credentials, then make a token at Github with the permissions required by Travis CI and pass it by specifying `--github-token` flag,
```yaml
travis login --github-token "some-token-code-here"
```
_Also, you have to register at **travis-ci.org/** [ or **travis-ci.com/**] and link your appropriate Github account with your account at Travis CI and give it the permissions required **before** trying to login_..
>**Note**:  

- If you've already logged in, you don't need to log in again.  
- If you have logged into other account, then run,
```yaml
travis logout
```
and then proceed the login step with the appropriate account.  

**5**-) There, encrypt your PyPI password,
```yaml
travis encrypt "myPyPIpassword"
```
Lastly, you have escape the special characters inside your password while encrypting.  
Travis CI accepts every non-alphanumeric character as a special character, such as paranthesis is a special character [for them].  
So, if your password is `"diFFicult(passWord%_19"`, then it must be escaped as,
```yaml
travis encrypt "diFFicult\(passWord\%\_19"
```
Yes, use either a single-backslash or double-backslashes for escaping characters.
  
**6**-) If you miss some step or apply is improperly, then Travis CI will never notify you and return you an encrypted password.
Which you will use and then realize that it does not work!
Only then will try to encrypt the password again..

What I want to mean is, Travis CI handles it really badly and you should follow those steps really correctly in the given order, otherwise it will confuse you.  

**7**-) If you have used the newly opened Travis CI server- **travis-ci.com/** which is much more enterprise-oriented rather than old **travis-ci.org/** server (_which seems to be deprecated after a while_) then you should add the `--pro` flag while using **travis** commands.  
Such as,
```yaml
travis login --pro
```

For your notice, here are the official flags of that specification which you can use,

+ `--pro` or `--com` : for **travis-ci.com/**
+ `--org` : for **travis-ci.org/** (_which currently is the **travis** tool's default.._)

Just run 
```yaml
travis login --help
```
to view those _flags_ and much more.

## Github releases
   
From now on, Github releases will also initiated with the same version (_tag_) we will have at PyPI releases.

### parallel releasing with PyPI
    
It is done gonna be initiated by tagging a specific time in commit history.
```yaml
git tag 0.1.1
```

And then pushing it
```yaml
git push --tags
```

Once you push a git tag [or tags], it automatically makes a release [or releases] at Github.  
And, at that time Travis CI will try to auto-deploy it.  
You should put the same version into the __version__ variable inside the **__init__.py** file as tag you would push.  
So that, the version at PyPI and the version (_tag_) in Github will be the same for the same release.


To see the already released tags in git,
```yaml
git tag
```
command will list them.

## Discord webhook
   
The idea is sending messages on build status of Travis CI to our Discord channel #status.  
But Travis CI does not like to add a support for Discord and there is a PR which enables it being laid there for quite a long time unmerged or replaced..

### custom script used

At the time I thought it was impossible, I heard about webhooks.  
Well, Discord gives us webhooks which can be used to send channels messages.  
And there is a nice Github repository doing that for Travis CI - https://github.com/DiscordHooks/travis-ci-discord-webhook.

It gives the full strength of Travis CI's possible official solution.  
And in our usage, it will send the message after each of the jobs being run and say if they have been passed or failed.

I have considered all cases and find using an external script [even from an open-source repository] being secure and to be used without problems.  
If you disagree and has a reason, please let me know, so that we can remove it.


_BTW Travis CI officially supports Slack.._  

Just a note, @timgrossmann, I have set an environmental variable in our Travis CI dot org account called `$DISCORD_WEBHOOK` with the value of the URL I got from our Discord's webhook I made enabled for the #status channel.

Recently, I found out that the solution we used is also shared at an official Travis CI webpage - https://docs.travis-ci.com/user/apps/ (_at the end of page in **Other** category_)


## Etc.

I could have forgotten lots of details, ask me if you can.


---

# Maintainers GUIDE on Auto & Manual deployments
  
Your files are great and you want to make a release at Github and also PyPI.

## Manually doing things
   
### How to make a [new] release at Github

**1**. _**Make sure**_ which release tags you already have by running
```yaml
git tag
```
which will give you the existing tags,
```yaml
0.1.0
0.1.1
```
so that you do not try to tag the same version again..  

**2**. Make a _**tag**_
```yaml
git tag 0.1.2
```
That means `0.1.2` tag holds state of your repository (_committed changes only_) up to the time it was tagged.  
If you make changes after tagging it and commit those changes or so,
that will not affect that tag.  

**3**. _**Pushing**_ tags
```yaml
git push --tags
```
That command automatically makes a new Github release if that already does not exist.

--- 

### How to publish a [new version of] package to PyPI
   
**1**. Change the _**version number**_ in the `__version__` variable at **__init__.py** file inside **instapy** package [folder] to the _**new**_ version number.
```python
__version__ = "0.1.2"
```

**2**. Then, you have to _**make distribution**_ files
```yaml
python setup.py sdist bdist_wheel
```
Running that command at the project root directory will make 2 new distribution archives for you at the **dist** folder.

**3**. _**Check**_ them with **twine** (_installed from **pip**_)
```yaml
twine check dist/*
```

**4**. _**Upload**_ them with **twine**
```yaml
twine upload dist/*
```
if that fails (_for some reasons.._),

```yaml
twine upload --repository-url https://pypi.org/legacy/ dist/*
```
You will have to enter your PyPI _username_ and _password_ in order to upload files.

>**Note**:
You might want to install **readme_renderer[md]** from **pip** for rendering **README.md** to upload to PyPI.

## Automatically doing things
   
### Travis CI deploy to PyPI on tags (<i>2 in 1</i> ☕)
    
**1**. _Change_ the _**version number**_ in the `__version__` variable at **__init__.py** file inside **instapy** package [folder] to the _**new**_ version number.
```python
__version__ = "0.1.2"
```

**2**. `add` and `commit` the change made to **__init__.py** regarding version increment and `push` it..
```yaml
git add .
git commit -m "new version: 0.1.2"
git push   # you don't have to push, but it would be better
```

**3**. Make _**a new** git_ _**tag**_ with the _exact_ version number you have just set in the 1st step,
```yaml
git tag 0.1.2
```

**4**. _**Push**_ that _**new tag**_
```yaml
git push --tags
```

**5**. **Ta Da** 🎉🎉🎉🎉🎉🎉🎉🎉🎉  
Github will make a new release by that tag and send a request to Travis CI that "_Hey bro, I've just received a new tag!_" and Travis CI will immidiately check,  

+ did the change came not from a _fork_?  
+ did the change came to `master` branch?  
+ does the tag match your pre-defined regex?  

If above is not true, then Travis CI will start building the jobs in 2 stages available- _static analysis_ and _test_ and, apparently, will not deploy to PyPI.  
But if all those above are true, then Travis CI will start building the jobs at all of the three stages available- _static analysis_, _test_ and then _deploy to PyPI_ automatically with the version you have set in `__version__` variable at **__init__.py** file included in the freshly tagged release.

---

### Advice [IMPORTANT]
    
- Always make sure the version to be released works good.  
Before incrementing the `__version__` at **__init__.py** and tagging that same version with git to push tags, make sure your code works really good and the build with the same code has passed before.  
I mean do anything you can- merge PRs or git push locally and see that build has passed.  
And only then, increment the version number and make a new tag with that new version and push that tag.  
It will make sure your build will surely pass and deployment will _succeed_ **always** 🎉

---

### Worse Situation <i>handling</i>
    
You push a tag to Github which ends up failing at _static analysis_ or _test_ stage and obviously - not being deployed to PyPI.  
At that point, you will end up with a new release at Github and not having that release deployed to PyPI 😥

**a**-) You can either push new commits to Github which will solve those problems and you build passes at Travis CI so that you are ready to deploy to PyPI.  
Then make the distribution files manually and upload that to PyPI by hand.  
Which will have your Github and PyPI stay at the same version in parallel.  
  
**b**-) You can first delete the release at Github (_it allows readding releases_) and then push the commits which will solve problems and have Travis CI build pass and are ready to deploy.  
Then make that release tag again and push it to Github once again.  
It will make a new Github release and also, obviously, pass static _analysis_ & _test_ stages and deploy to PyPI successfully.

---

# Final
Let me caption this from _Dustin Ingram_'s talk at **SciPy** _2018_,

"_Packaging isn't bad. There are always gonna be problems that need to be solved here_.  
_We've made very gradual changes here at period of time and each change has been a response to serve in an evolving need. Comparatively we used to have it really bad and we may have actually just forgotten and or may not just known how hard it used to be_.
_So the next time you are frustrated with python packaging, imagine a world with no **pip**, no PyPI, no conda and consider making a pull request_ 🙂"

_Dustin_ is a member of the PyPA.



Thanks to all of the people who wrote references/examples/tutorials on web (_esp. at SO_), official documentations for tools and helped me at IRC #python and uploaded video tutorials to YouTube 🥳




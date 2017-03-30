---
title: Git init 2.2：忽略文件
date: 2017-02-25 13:00:00
tags: git
---


## 忽略文件 .gitignore

操作系统的运行过程、开发环境的部分配置文件、编译器的编译过程，编译结束后生成的文件通常不需要提交到版本库中进行追踪，一方面此类文件可以通过源代码生成，另一方面反复提交编辑结果会导致版本控制仓库体积的剧增，不利于版本控制工具的维护。

Git使用`.gitignore`这个配置文件对不需要提交的文件进行忽略，达到节省开销的目的。

`.gitignore`文件在编辑时遵循以下原则：

* 以斜杠“/”开头表示目录；

* 以星号“*”通配多个字符；

* 以问号“?”通配单个字符

* 以方括号“[]”包含单个字符的匹配列表；

* 以叹号“!”表示不忽略(跟踪)匹配到的文件或目录；

需要注意以下几点：

* 匹配顺序为自上而下。

* `.gitignore`是工作目录的一部分，建议提交时对其进行更新。

* 已经被版本控制追踪的文件无法通过修改`.gitignore`文件进行忽略，如果需要在后续版本中忽略，请删除这个文件并把删除行为添加到缓存区进行提交。

* `.gitignore`的使用与选择不仅与开发语言有关，还会受到开发环境、编译器、平台等因素的影响。

Github的[github/gitignore](https://github.com/github/gitignore)项目中提供了一些语言及开发环境使用的`.gitignore`模板。

下面列出了C++语言使用的`.gitignore`模板：

```
# Prerequisites
*.d

# Compiled Object files
*.slo
*.lo
*.o
*.obj

# Precompiled Headers
*.gch
*.pch

# Compiled Dynamic libraries
*.so
*.dylib
*.dll

# Fortran module files
*.mod
*.smod

# Compiled Static libraries
*.lai
*.la
*.a
*.lib

# Executables
*.exe
*.out
*.app
```
Python的`.gitignore`文件：

```
# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# C extensions
*.so

# Distribution / packaging
.Python
env/
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# PyInstaller
#  Usually these files are written by a python script from a template
#  before PyInstaller builds the exe, so as to inject date/other infos into it.
*.manifest
*.spec

# Installer logs
pip-log.txt
pip-delete-this-directory.txt

# Unit test / coverage reports
htmlcov/
.tox/
.coverage
.coverage.*
.cache
nosetests.xml
coverage.xml
*,cover
.hypothesis/

# Translations
*.mo
*.pot

# Django stuff:
*.log
local_settings.py

# Flask stuff:
instance/
.webassets-cache

# Scrapy stuff:
.scrapy

# Sphinx documentation
docs/_build/

# PyBuilder
target/

# Jupyter Notebook
.ipynb_checkpoints

# pyenv
.python-version

# celery beat schedule file
celerybeat-schedule

# SageMath parsed files
*.sage.py

# dotenv
.env

# virtualenv
.venv
venv/
ENV/

# Spyder project settings
.spyderproject

# Rope project settings
.ropeproject
```

---
title: Poetry - Create Environments and Manage Dependencies
description: Instructions on how to quickly setup a new development environment, setup jupyter and solve some common issues. Also contains a few useull links.
date: 2025-08-16 18:55:00 +0200
categories: [Documentation, Code Management]
tags: [poetry, package management, python]     # TAG names should always be lowercase
image:
  path: /assets/img/icon.jpg
  lqip: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD/4QAiRXhpZgAATU0AKgAAAAgAAQESAAMAAAABAAEAAAAAAAD/2wBDAAYEBQYFBAYGBQYHBwYIChAKCgkJChQODwwQFxQYGBcUFhYaHSUfGhsjHBYWICwgIyYnKSopGR8tMC0oMCUoKSj/2wBDAQcHBwoIChMKChMoGhYaKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCj/wAARCAAUACADASIAAhEBAxEB/8QAHwAAAQUBAQEBAQEAAAAAAAAAAAECAwQFBgcICQoL/8QAtRAAAgEDAwIEAwUFBAQAAAF9AQIDAAQRBRIhMUEGE1FhByJxFDKBkaEII0KxwRVS0fAkM2JyggkKFhcYGRolJicoKSo0NTY3ODk6Q0RFRkdISUpTVFVWV1hZWmNkZWZnaGlqc3R1dnd4eXqDhIWGh4iJipKTlJWWl5iZmqKjpKWmp6ipqrKztLW2t7i5usLDxMXGx8jJytLT1NXW19jZ2uHi4+Tl5ufo6erx8vP09fb3+Pn6/8QAHwEAAwEBAQEBAQEBAQAAAAAAAAECAwQFBgcICQoL/8QAtREAAgECBAQDBAcFBAQAAQJ3AAECAxEEBSExBhJBUQdhcRMiMoEIFEKRobHBCSMzUvAVYnLRChYkNOEl8RcYGRomJygpKjU2Nzg5OkNERUZHSElKU1RVVldYWVpjZGVmZ2hpanN0dXZ3eHl6goOEhYaHiImKkpOUlZaXmJmaoqOkpaanqKmqsrO0tba3uLm6wsPExcbHyMnK0tPU1dbX2Nna4uPk5ebn6Onq8vP09fb3+Pn6/9oADAMBAAIRAxEAPwD0DTvNlvmuxd3FurtndGc8D+HJ6j/GsPxN49fU5jpujXMJSMkSTMMqx9Bz096zPEviS5h0R7K0tfsd5OPKUEEkZ+8wHsM/jXK+D/Dy2+qG01YSB9waRSdpJ4Od3p9K8Oi3FSs91odVNRUk5LQ7O80L7VpbS3sG7cBukh5Ckj3rlI/hvFdTxyvd/uQQxDcGvWVsYLJHj84yecAoAYkKvYY/xzWP4hszaLm0AcuNp5xx7Uqdao043s0KUYyk2tjx7QriW5ub1pXBNnHIyfKPmweh46HJ6YrvNOthqNtJezyzC4t7fy4nRzlRwep5PLH2oop1G4y0MzT8EXz6nNcQ3yJKPMdGfLKzAY7gjH4Yro9OtIUk8vaXEbFkLsWI9sntRRWNbSat3CJ//9k=
  alt: An initial post for testing the blog features
---

Poetry is a versatile tool to quickly setup an isolated environment for Python. 
We can use Poetry to manage different environments, prepare custom packages for release
and package development dependencies for testing and deployment.

> [Poetry](https://python-poetry.org/docs/) is a tool for dependency management and packaging in Python. It allows you to declare the libraries your project depends on and it will manage (install/update) them for you. Poetry offers a lockfile to ensure repeatable installs, and can build your project for distribution.

We can use Poetry for a few things when building testing infrastructure or custom
container images. First, we can include different types of dependencies and generate
a single project file to document all required packages. This allows us to track 
all explicit dependencies and integrate them with git and similar version management
tools. Second, Poetry uses a common cache for all local projects. Each project 
typically creates a custom environment to manage runtime dependencies, reducing 
risk of sudden breaking changes. We can use this when comparing different tools or 
when setting up specific environments for testing.  


## The tl;dr

To create a basic installation for poetry on most Linux systems, run the
following bash script to setup Poetry inside an isolated Python environment. 
By configuring the installation path, Poetry can be setup in any folder. Using 
`export POETRY_HOME=/opt/poetry` to change the default installation path.

```bash
POETRY_HOME=${POETRY_HOME:-/opt/poetry} 
POETRY_VERSION=${POETRY_VERSION:-"2.1.2"} 
sudo mkdir -p $POETRY_HOME
sudo chown $(id -u):$(id -g) $POETRY_HOME
python3 -m venv $POETRY_HOME
$POETRY_HOME/bin/pip install poetry==$POETRY_VERSION
$POETRY_HOME/bin/poetry --version
```

To use the created environment: 

```bash
$POETRY_HOME/bin/poetry init   # generate a new poetry project (interactive)
$POETRY_HOME/bin/poetry add <some_python_package>   # add dependencies
eval $($POETRY_HOME/bin/poetry env activate)   # run a local shell 
$POETRY_HOME/bin/poetry run some_file.py   # execute some file with poetry dependencies
```

Depending on the use case, it might be usefull to create a new alias to Poetry.

```
alias poetry="$POETRY_HOME/bin/poetry"
```

When generating or using a new project file (pyproject.toml), remember to disable 
package mode (`package-mode = false`), when using Poetry for environment management. 

## First time Installation and Configuration

Poetry needs to be installed inside an isolated environment. Therefore we typically 
use venv inside a custom location to separate the installation from the default 
system managed environment. Since Poetry manages different core dependencies
it could potentially create a conflict when updating itself or when updating the 
system through pip or a package manager. To reduce the risk of breaking dependencies
or having different package managers interfere with each other, poetry should always
be used outside of the default system installation. Also important: Do **not** run a
project from the poetry environment! This will also mess with dependencies, in case
there is a mismatch between packages.

The documentation recommend to install Poetry inside an isolated venv and to 
run all commands from there:

```bash
# needed for porper venv setup with some linux distros
# sudo  apt install python3.xx-venv
# apt/pip will usually show, if the venv package is missing

# setup the default poetry path for the poetry environment
POETRY_HOME=${POETRY_HOME:-/opt/poetry} 
POETRY_VERSION=${POETRY_VERSION:-"2.1.2"} 
sudo mkdir -p $POETRY_HOME
sudo chown $(id -u):$(id -g) $POETRY_HOME

# create a custom environment to store poetry specific configuration
python3 -m venv $POETRY_HOME

# install the required version of poetry inside the new venv and run poetry
$POETRY_HOME/bin/pip install poetry==$POETRY_VERSION
$POETRY_HOME/bin/poetry --version
```

To start a new custom project we need to go into a project folder and create/reload an 
environment.

```bash
cd /To/Our/Project

# create a new environment with shell, given we have a pyproject.toml 
eval $(poetry env activate)
python myscript.py
```

By starting a shell or a subcommand, all dependencies are referenced and updated inside the subshell.


Poetry evaluates the current shell, if there is any active venv for the current session. It poetry detects an active session, this session will be reused to install and manage all dependencies that are found inside the toml/lock files. 
By running poetry with `$POETRY_HOME/bin/poetry` the active shell does not run an venv session and poetry uses a automatically created venv environment for this session.
By running `$POETRY_HOME/bin/poetry shell` this session will be opened with a custom subshell inside the active shell session.
this way we can use different virtual environments with poetry and load custom environments when starting a script or a new session.

Typical usage of poetry could look like this;
```bash
export POETRY_HOME=/opt/poetry
$POETRY_HOME/bin/poetry add jupiter notebook
$POETRY_HOME/bin/poetry add ipykernel

$POETRY_HOME/bin/poetry show --tree
$POETRY_HOME/bin/poetry install # needed?

# with these poetry spawns a local shell or a notebook and runs all commands isolated inside a new venv
$POETRY_HOME/bin/poetry run jupyter notebook
# or
$POETRY_HOME/bin/poetry shell
```

Poetry displays the currently available environments for a single project with `env list`. The command must be executed within a project folder:
```bash
$POETRY_HOME/bin/poetry env list
```

> Poetry indicates which of the virtual environments connected to your project is currently selected as the **default one**. Although it says _activated_, the corresponding virtual environment isn’t actually activated in your shell in the traditional sense. Instead, Poetry will temporarily activate that virtual environment in a [subprocess](https://realpython.com/python-subprocess/) when you run one of the Poetry commands. [^5]

# Setting up a new Project



```bash
$POETRY_HOME/bin/poetry new poetry-demo
```


```text
poetry-demo
├── pyproject.toml
├── README.md
├── poetry_demo
│   └── __init__.py
└── tests
    └── __init__.py
```


```toml
[project]
name = "poetry-demo"
version = "0.1.0"
description = "This is a simple demo file"
authors = []
readme = "README.md"
requires-python = ">=3.9"
package-mode = false
dependencies = []
```



## Manage Dependencies 

In order to add new dependencies it is best to use the command line interface 
and the provided CLI to update the pyproject.toml. This way any dependency conflicts 
can be catched early.

```bash
poetry add <package names whitespace separated>
```

To remove existing or unused dependencies the `remove` subcommand can be used.

```bash
poetry remove <package names whitespace separated>
```

In order to manage specific version ranges or enforce a fixed version on a dependency
custom constraints can be used.

```bash
$POETRY_HOME/bin/poetry add requests==2.25.1 "beautifulsoup4<4.10"
```

For development setups or testbeds sometimes online sources from upstream 
projects are required to build or integrate packages. The `add` subcommand 
supports the addition of remote urls to fetch and integrate version controlled 
sources. Depending on the integration tarballs or custom repositories can be enabled.

```
$POETRY_HOME/bin/poetry add https://github.com/FreeOpcUa/opcua-asyncio/archive/refs/tags/v0.9.95.tar.gz
```

## Run a Project on a new System

If Poetry is already set up correctly, we can use a project file to generate and 
initialize a new environment. By using the `install` subcommand a new venv is 
created inside the Poetry cache directory and the current working directory is 
added to the managed cache.  

```bash
poetry install
```

## Setup Jupyter Notebook with Poetry

Jupyter usually does not display an extra kernel in the web view, if the
installation is not setup correctly. To use Jupyter with Poetry, all dependencies 
must be created and installed directly with poetry inside the local environment
of the project ([Source](https://stackoverflow.com/questions/72434896/jupyter-kernel-doesnt-use-poetry-environment)).

```bash
poetry add jupyter notebook # run a project live inside the poetry venv
poetry add ipykernel
poetry show --tree
poetry install 
```

### Outline:

Create a new project with Poetry:

```python
poetry new test_ipynb
cd test_ipynb
```
Use a specific Python version with your environment:

```python
poetry env use 3.12
```

Add ipykernel to your environment:

```python
poetry add ipykernel
```

Check your environment's executable path:

```python
poetry env info
```


## Some Common Issues 
---

These are some known issues, happening with versions from 1.8.x to 2.x.x. Most
issues found with poetry will likely also be found in the issue tracker over at 
GitHub. Also a lot of issues can be resoved by checking the current version of 
poetry and updating the core version.


### Lock Up during Installation or Updates

There is a known issue, where poetry tries to call the existing keyring when running 
inside Docker or other Debian based installations. In some instances the keyring 
is not accessible by poetry, which in turn locks up the entire process.

When using these images when setting up a project or resolving dependencies,
the internal keyring needs to be disabled: 

```bash
$POETRY_HOME/bin/poetry config keyring.enabled false
```

Sometimes we also need to configure the keyring and pkginfo inside poetry (for version 1.8.3):

```bash
$POETRY_HOME/bin/poetry config keyring.enabled false
$POETRY_HOME/bin/poetry self add keyrings.cryptfile@latest && $POETRY_HOME/bin/poetry self add pkginfo==1.10.0
```


### bash-completion issues syntax errors on startup 

When connecting to a shell managed by poetry or an environment that uses poetry components, there is a chance that older versions of poetry broke existing bash completion scripts. This issue[^3] has been resolved with version *1.3.0*. Important: Pip ships an old version of poetry, the included self updater needs to be used to get a version > 1.2.x. Fix:

```bash
$POETRY_HOME/bin/poetry self update
$POETRY_HOME/bin/poetry completions bash > /usr/local/etc/profile.d/poetry.bash-completion
```

This recreates the broken file and fixes issues with older versions of poetry.

### Sourcing from setup scripts

Setting up poetry package management for python projects when using a script to export all important variables (POETRY_HOME) and features.
E. g.:
```
source source_poetry.sh
```

[^1]: https://stackoverflow.com/questions/16011245/source-files-in-a-bash-script
[^2]: https://python-poetry.org/docs/
[^3]: https://github.com/python-poetry/poetry/issues/4572
[^4]: https://github.com/python-poetry/poetry
[^5]: https://realpython.com/dependency-management-python-poetry/

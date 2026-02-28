+++
title = 'Setting Up a Local Environment Using Pyenv and venv'
description = 'How to set up a Python development environment on Mac using Pyenv and venv. Covers version management, virtual environment creation, updates, and deletion.'
date = 2023-12-10T23:19:33+09:00
lastmod = 2023-12-10T23:19:33+09:00
draft = false
categories = ['Engineering']
tags = ['Python', 'mac']
aliases = ['/en/blog/004-paython-setup/']
+++

## Overview
This article documents how to set up a Python environment for development on a Mac local environment.  
In this case, we will use two systems to manage different versions of Python and virtual environments:

* pyenv
  * Used to handle multiple versions of Python.
* venv
  * Used to separate environments for each project.

For explanations on the differences and the necessity of each, this [article](https://jimaru.blog/programming/python/venv_pyenv_choice/) is a helpful reference.

## Installing Python
First, install Pyenv on your local environment to use a specific version of Python.

Install pyenv.

```shell
brew install pyenv
```

Check the installed version of pyenv.

```shell
pyenv --version
pyenv 2.3.35
```

Add settings to zsh.

```shell
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc    
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
```

Reload `.zshrc`.

```shell
source ~/.zshrc
```

Display a list of installable Python versions.

```shell
pyenv install --list
```

Install the specified version.

```shell
pyenv install 3.11.7
```

Use the specified Python version in your project folder.

```shell
cd <created project folder>
pyenv local 3.11.7
pyenv versions
```
If global, it will be applied to the entire system.

```shell
pyenv global 3.11.7
```

Check the version of Python being executed.

```shell
python -V
```

## Creating a Virtual Environment with venv

Create a virtual environment in the project directory.

```shell
# python -m venv <virtual environment name>
python -m venv venv
```

Activate the virtual environment.

```shell
source venv/bin/activate
```

To deactivate, execute the following command.

```shell
deactivate
```

## How to Update a venv

If you need to refresh dependencies, activate the virtual environment and run the following. When using `requirements.txt`, update the file first and then reinstall.
```shell
source venv/bin/activate
pip install --upgrade pip
pip install -U -r requirements.txt
```

When upgrading Python to a new minor version, install the new version with `pyenv`, recreate the virtual environment, and reinstall dependencies to avoid mismatches.
```shell
pyenv install 3.12.2
pyenv local 3.12.2
rm -rf venv
python -m venv venv
source venv/bin/activate
pip install -U -r requirements.txt
```

## How to Delete a venv

When the virtual environment is no longer needed, delete the directory. Confirm you are in the correct working directory before running the command.
```shell
rm -rf venv
```

This completes the setup of the local environment.

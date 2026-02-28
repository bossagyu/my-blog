+++
title = 'Setting Up TypeScript Development Environment Easily with Volta'
description = 'How to set up a TypeScript development environment on Mac with Volta. Covers Node.js installation, yarn setup, project creation, and Hello World execution.'
date = 2024-03-10T13:11:13+09:00
lastmod = 2024-03-10T13:11:13+09:00
draft = false
categories = ['Engineering']
tags = ['TypeScript', 'Node.js']
+++

## Overview
This article explains how to easily set up a TypeScript development environment using Volta, with MacOS as the target platform.

## What is Volta?

Volta is a version management tool for Node.js, featuring the following characteristics as presented on [Volta's official site](https://volta.sh/):

* Fast: Built in Rust, enabling swift Node.js version switching.
* Reliable: Ensures everyone on a project uses the same tools.
* Universal: Can be used across package managers, node runtimes, and OSes.

While nodebrew has been commonly used, the use of Volta seems to be increasing lately.

## Installing Volta and Node.js

Installing Volta is as simple as running the following command:

```shell
curl https://get.volta.sh | bash
```

If you're using zsh and the path hasn't been automatically set, use the following commands:

```shell
echo 'VOLTA_HOME=$HOME/.volta' >> ~/.zshrc
echo 'export PATH=$VOLTA_HOME/bin:$PATH' >> ~/.zshrc
source ~/.zshrc
```

Verify the installation. If a version is displayed, Volta has been successfully installed:

```shell
volta -v
```

Next, use Volta to install Node.js. If no version is specified, the latest LTS version will be installed:

```shell
volta install node
```

## Installing yarn and Creating a TypeScript Project

### Differences between npm and yarn

Both npm and yarn are package managers for Node.js. Their features include:

**npm**:
- Released a year after Node.js (2010).
- Stands for Node Package Manager.
- Automatically generates a package-lock.json file.
- Installed automatically with Node.js.

**yarn**:
- Released in 2016.
- Developed by Facebook, Google, Exponent, and Tilde.
- Compatible with npm (can use the same package.json).
- More strict in locking module versions than npm.
- Faster installation than npm.

While yarn appears superior, recent updates to npm have minimized the differences. Here, we will use yarn to create a TypeScript project.

### Installing yarn

Install yarn using Volta:

```shell
volta install yarn
```

Confirm the installation by checking if yarn is listed:

```shell
volta list
```

### Creating a TypeScript Project

Initialize yarn:

```shell
yarn init -y
```

Install Node.js:

```shell
volta pin node@20.0.0
```

Install TypeScript:

```shell
yarn add typescript
```

Install ts-node:

```shell 
yarn add --dev ts-node
```

Create a tsconfig.json file. This is a TypeScript configuration file that details compilation settings. In this case, you can leave it at the default settings generated:

```shell
yarn tsc --init
```

Test a sample program:

```shell
echo "console.log('Hello, TypeScript!');" > hello.ts
yarn ts-node hello.ts

# If "Hello, TypeScript!" is displayed, it's a success.
```

The test script ran successfully. This completes the setup of the TypeScript development environment.

## Summary
This article described how to set up a TypeScript development environment easily using Volta. By using Volta, managing Node.js versions becomes straightforward, making the development environment setup smoother. Additionally, specifying the Node.js version with Volta in your project's package.json can help eliminate version discrepancies among developers, adding another layer of convenience to your workflow.
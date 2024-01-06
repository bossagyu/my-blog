+++
title = 'Publishing a Blog with Hugo + Netlify + Github'
date = 2023-12-02T00:59:37+09:00
draft = false
toc = true
categories = ['Engineering']
tags = ['Hugo', 'Netlify', 'Github']
+++
## Overview
This document describes the steps to create a site with Hugo, manage it with Github, and build it with Netlify from scratch.  
With this method, you can easily publish by just pushing your Markdown-written blog to Github.

## Process
1. Generate a site with Hugo
2. Push to Github
3. Deploy with Netlify

## Generating a Static Site with Hugo
First, install Hugo.
```shell
brew install hugo
```

Create a template for the blog.
```shell
hugo new site my-blog
```

Add a theme suitable for the blog as a submodule.
```shell
cd my-blog
git init

# Add the theme as a submodule from Github
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

Apply the theme by adding it to hugo.toml.
```shell
echo "theme = 'ananke'" >> config.toml
```

Start the server.
```shell
hugo server
```

Access the URL like `http://localhost:51517/` shown in the startup log `Web Server is available at http://localhost:51517/ (bind address 127.0.0.1)` to view the locally launched static site.

### Tips
* If you want to change the Hugo theme, please choose your favorite one from [Hugo Themes](https://themes.gohugo.io/).
  * It's recommended to run through until you build with Netlify first, as this can be changed later.
* The way to write Toml files is described in [Configure Hugo](https://gohugo.io/getting-started/configuration/).

<br>

## Push to Github

Create a repository on [Github](https://github.com/).<br>
After creation, execute the following commands to push your site.
```shell
cd my-blog
echo .hugo_build.lock >> .gitignore
git add .
git commit -m "first commit"
git branch -M main

# Replace <user name> with your own username.
# This is an example of creating a repository called my-blog.
git remote add origin git@github.com:<user name>/my-blog
git push -u origin main
```

Once the push is complete, the source code becomes viewable on the Github UI.


## Deploy with Netlify
Access [Netlify](https://www.netlify.com/) and perform deployment.  
There are instructions on [Hugo's official website](https://gohugo.io/hosting-and-deployment/hosting-on-netlify/), so refer to them for integration.

Follow the instructions to complete the deployment, and the result of the Deploy will be shown as `published`.

![img-001-001.png](img-001-001.png)


Click on the URL displayed on the site to access the deployed site.
![img-001-002.png](img-001-002.png)


This completes the deployment process.
After this, any changes made and pushed to main will automatically trigger deployment, updating the site content.
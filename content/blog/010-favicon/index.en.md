+++
title = 'Creating and Displaying a Favicon with Hugo'
description = 'How to create a favicon and set it up on a Hugo blog. Covers image generation with Favicon Generator and configuration in config.toml.'
date = 2023-12-24T22:14:39+09:00
draft = false
categories = ['Engineering']
tags = ['Hugo', 'Favicon']
+++

## Overview
This article explains a simple method to create a favicon. It also covers how to display a favicon in Hugo.

## What is a Favicon?
A favicon is an icon that appears in bookmarks, tabs, and home screens for websites. Google has published [guidelines for favicons](https://developers.google.com/search/docs/appearance/favicon-in-search?hl=ja#guidelines) that appear in search results. Adhering to these guidelines can help your favicon appear in search results.

## Creating a Favicon
To create a favicon, use the following site:

* [Favicon.ico & App Icon Generator](https://www.favicon-generator.org/)

When you visit the site, you'll see a screen like this.  
![Favicon Generator](img-010-001.png)

Enter the URL of the site you want a favicon for and click on Generate Favicon. Then, on the displayed screen, click the 'Download the generated favicon' link to download the favicon.

## Displaying a Favicon in Hugo
To display a favicon in Hugo, the process varies depending on the theme. For the [bearcub theme](https://github.com/clente/hugo-bearcub/tree/main), you can simply set it in the toml like this:

```shell
[params]
  favicon = "images/favicon.ico"
```

## Summary
This article explained how to create a favicon and display it in Hugo. Favicons are displayed in bookmarks, tabs, and home screens, so it's a good idea to create one for your blog.
+++
title = 'How to Set Up Google Analytics with Hugo'
description = 'How to set up Google Analytics GA4 on a Hugo blog. Covers tracking ID setup, config.toml configuration, and embedding analytics tags in templates.'
date = 2023-12-09T18:09:42+09:00
lastmod = 2023-12-09T18:09:42+09:00
draft = false
categories = ['Engineering']
tags = ['Hugo', 'SEO', 'Google Analytics']
+++

## Overview
This article briefly explains how to set up Google Analytics with Hugo.

## Steps to Implementation
1. Register with Google Analytics
2. Obtain the Tracking ID
3. Add the Tracking ID to Hugo's configuration

## Registering with Google Analytics
Follow the instructions on [Setting up a new website or app with GA4](https://support.google.com/analytics/answer/9304153?hl=ja) to register.  
When you add a data stream, you will get a Tracking ID, so make a note of it.
![Add Data Stream Screen](img-003-001.png)
※ The Tracking ID may be displayed as "Measurement ID" due to translation.

## Adding Tracking ID to Hugo's Configuration
### Add settings in toml
Add `googleAnalytics = Tracking ID` to your config.toml.

```toml
baseURL = 'https://bossagyu.com'
languageCode = 'ja-jp'
title = 'Bossagyu Blog'
theme = 'hugo-bearcub'
googleAnalytics = "G-1234ABCDEF"
# ↑ Add this line, replace the Tracking ID with your own.
```

### Embedding the Tracking Code
Some templates might read settings from the toml file, but the [bearcub](https://github.com/clente/hugo-bearcub/tree/main) template I use does not support this, so I added the tracking code to the header myself.

For the code snippet, I referred to [Makumaku Hugo Notes](https://github.com/clente/hugo-bearcub/tree/main).

Create `layouts/partials/analytics.html` to load the tracking code.

```html
{{ if not .Site.IsServer }}
{{ with .Site.GoogleAnalytics }}
<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id={{ . }}"></script>
<script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());

    gtag('config', '{{ . }}');
</script>
{{ end }}
{{ end }}
```

Make the page header load `analytics.html`.

```shell
# Copy the content of the template to override it
cp themes/hugo-bearcub/layouts/_default/baseof.html layouts/_default/baseof.html 
vim layouts/_default/baseof.html 
```

Add `{{- partial "analytics" . -}}` to baseof.html.

```html
<!DOCTYPE html>
<html lang="{{ with .Site.LanguageCode }}{{ . }}{{ else }}en-US{{ end }}">

<head>
  {{- partial "analytics" . -}}
  <meta http-equiv="X-Clacks-Overhead" content="GNU Terry Pratchett" /
```

After adding the above source code and rebuilding, data will be sent to Google Analytics.

## Tips

If it seems like data is not being transmitted to Google Analytics despite these steps, it's possible that the tags have not been added correctly.  
To troubleshoot, first check if the tracking is included in the HTML by using Google Developer Tools.

## Related Articles

- [Publishing a Blog with Hugo + Netlify + Github](/blog/001-hugo-netlify-build/) - Blog initial setup
- [Using Google Search Console to Make Your Blog Searchable on Google](/blog/007-google-search-console/) - SEO setup
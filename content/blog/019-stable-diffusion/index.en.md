+++
title = 'How to Use Stable Diffusion Web UI on Mac'
date = 2024-02-12T11:24:59+09:00
draft = true
categories = ['Engineering']
tags = ['Stable Diffusion', 'AI']
+++

## Overview
This article will guide you through installing Stable Diffusion Web UI on Mac and using it locally.

## What is Stable Diffusion?
Stable Diffusion is a type of image processing technology using AI. By inputting text, it can generate images corresponding to that text.

## Ways to Use Stable Diffusion

There are two main ways to use Stable Diffusion:

* Using web applications like [Hugging Face](https://huggingface.co/) or [Dream Studio](https://beta.dreamstudio.ai/generate)
* Using Stable Diffusion Web UI locally

This article focuses on using Stable Diffusion Web UI locally. While web applications are easy for trial purposes, they might have limitations or costs for generating a significant number of images. Thus, local usage is recommended for more extensive needs.

## How to Use Stable Diffusion Web UI Locally
We will use [stable-diffusion-web-ui](https://github.com/AUTOMATIC1111/stable-diffusion-webui) published by AUTOMATIC1111 for this purpose.

1. Prepare the environment
2. Install stable-diffusion-web-ui
3. Place the model files
4. Start stable-diffusion-web-ui and generate images

### 1. Preparing the Environment
First, we need to install Python and other necessary libraries using homebrew.

Install homebrew:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Set the homebrew path:

```bash
export PATH="$PATH:/opt/homebrew/bin/"
```

Install related libraries:

```shell
brew install cmake protobuf rust pyenv git wget
```

Set up the Python environment using pyenv. This allows you to use multiple versions of Python. For building the Python environment, refer to [this article](https://bossagyu.com/blog/004-paython-setup/).

```shell
pyenv install 3.10.6
pyenv local 3.10.6
```

### 2. Installing stable-diffusion-web-ui
Clone the repository using git clone:

```shell
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
cd stable-diffusion-webui
```

Set up a virtual environment using venv to keep the environment clean:

```shell
python -m venv venv
source venv/bin/activate
```

Now the environment is ready.

### 3. Placing the Model Files
Next, download the model files and place them in the `stable-diffusion-webui/models/Stable-diffusion/` directory. Model files can be downloaded from sites like:

* [Civitai](https://dream-studio.tech/)
* [Hugging Face](https://huggingface.co/models)

For this example, we'll download the `blue_pencil` model from Civitai.

Search for `blue_pencil` in Civitai's search bar, select `blue_pencil` from the search results, and click the `Download` button.

![Download Screen](img-019-001.png)

Move the downloaded model to the directory:

```shell
mv ~/Downloads/bluePencilXL_v401.safetensors models/Stable-diffusion/
```

### 4. Starting stable-diffusion-web-ui and Generating Images
Finally, start stable-diffusion-web-ui and generate images:

```shell
./webui.sh
```

After launching, input the text in the prompt and generate images:

1. Select the downloaded `blue_pencil` model in Stable Diffusion checkpoint.
2. Enter elements you want in the image in the prompt.
3. Enter elements you don't want in the image in the Negative prompt.
4. Click Generate.

![Image Generation Screen](img-019-002.png)

By inputting "a dog wearing pink sunglasses," the output matched the input. You can generate various images by inputting different texts.

## Summary
This article introduced how to install and use Stable Diffusion Web UI on Mac locally. Using it locally allows you to generate images freely without the limitations or costs associated with web applications.
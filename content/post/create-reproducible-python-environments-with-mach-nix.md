---
title: "Create Reproducible Python Environments With mach-nix"
date: 2023-06-24T21:58:37+05:30
lastmod: 2023-06-24T21:58:37+05:30
draft: false
keywords: [mach-nix python]
description: ""
tags: [nix]
categories: [nix]
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: false
toc: true
autoCollapseToc: false
postMetaInFooter: true
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: false

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: false
  options: ""

sequenceDiagrams: 
  enable: false
  options: ""

---

Python is a popular and versatile programming language that is widely used for data science, web development, machine learning and more. However, managing python environments and dependencies can be a challenging task, especially when you want to ensure reproducibility and portability across different platforms and machines.

One common way to create python environments is to use tools like pip, virtualenv, pipenv or poetry. These tools allow you to specify your dependencies in a requirements.txt file or a similar format, and then install them in a virtual environment. However, these tools often suffer from some drawbacks, such as:
<!--more-->

- They rely on the availability and consistency of online repositories, which may change or disappear over time.
- They do not guarantee that the same versions of packages and their dependencies will be installed on different machines or platforms.
- They do not handle non-python dependencies or system libraries very well.
- They may require additional tools or layers of virtualization to work properly.

Another way to create python environments is to use nix, a revolutionary build system that is known to achieve great reproducibility and portability. Nix allows you to declare your dependencies in a nix expression, and then build them in a sandboxed environment that is isolated from the rest of the system. Nix also caches the build results and allows you to share them with others. However, nix also has some drawbacks, such as:

- It has a steep learning curve and a different syntax than python.
- It requires you to write nix expressions for each python package you want to use, which can be tedious and error-prone.
- It may not support all the python packages or versions that you need.

This is where mach-nix comes in. Mach-nix is a project that aims to make it easy to create and share reproducible python environments or packages using nix. Mach-nix can install packages from three different sources: pypi, conda and nixpkgs. It also supports hardware optimizations, cross platform support, private packages and tweaking build parameters. You can use mach-nix with a simple requirements.txt file or a more advanced nix expression.

In this blog post, I will show you how to use mach-nix to create a python environment for a simple web application that uses Flask and requests.

## Installing mach-nix

To use mach-nix, you need to have nix installed on your system. You can install nix by following the instructions on https://nixos.org/download.html.

Once you have nix installed, you can install mach-nix by running the following command:

```bash
nix-env -iA mach-nix -f https://github.com/DavHau/mach-nix/tarball/3.5.0
```

This will install mach-nix version 3.5.0 on your system. You can check the latest version of mach-nix on its GitHub page: https://github.com/DavHau/mach-nix.

## Creating a requirements.txt file

The simplest way to use mach-nix is to create a requirements.txt file that lists the python packages you want to use. For example, for our web application, we need Flask and requests. We can create a requirements.txt file with the following content:

```txt
Flask
requests
```

## Creating a python environment with mach-nix

To create a python environment with mach-nix, we can use the `mach-nix.mkPython` function. This function takes a list of requirements as an argument and returns a derivation that contains the python interpreter and the installed packages.

We can write a nix expression that uses `mach-nix.mkPython` as follows:

```nix
let
  # Import mach-nix
  mach-nix = import (builtins.fetchGit {
    url = "https://github.com/DavHau/mach-nix";
    ref = "refs/tags/3.5.0";
  }) {};
in
  # Create a python environment with our requirements
  mach-nix.mkPython {
    requirements = builtins.readFile ./requirements.txt;
  }
```

We can save this expression in a file called `default.nix`.

## Building and entering the python environment

To build and enter the python environment, we can run the following command:

```bash
nix-shell default.nix
```

This will download and build the required packages and then drop us into a shell where we can access the python interpreter and the installed packages.

We can verify that we have Flask and requests available by running:

```bash
python -c "import flask; import requests; print(flask.__version__, requests.__version__)"
```

This should print something like:

```bash
2.0.2 2.26.0
```

## Writing a simple web application

Now that we have our python environment ready, we can write a simple web application that uses Flask and requests. For example, we can create a file called `app.py` with the following content:

```python
from flask import Flask, request, jsonify
import requests

app = Flask(__name__)

@app.route("/")
def index():
    return "Hello, world!"

@app.route("/weather")
def weather():
    city = request.args.get("city", "London")
    api_key = "your_api_key_here"
    url = f"https://api.openweathermap.org/data/2.5/weather?q={city}&appid={api_key}"
    response = requests.get(url)
    data = response.json()
    return jsonify(data)
```

This web application has two routes: `/` and `/weather`. The `/` route returns a simple greeting message. The `/weather` route takes a city name as a query parameter and returns the current weather data from the OpenWeather API. You need to replace `your_api_key_here` with your own API key, which you can get from https://openweathermap.org/api.

## Running the web application

To run the web application, we can use the `flask` command from our python environment:

```bash
FLASK_APP=app.py flask run
```

This will start a development server on http://localhost:5000.

We can test our web application by opening http://localhost:5000 in a browser or using a tool like curl:

```bash
curl http://localhost:5000
```

This should return:

```bash
Hello, world!
```

We can also test the `/weather` route by passing a city name as a query parameter:

```bash
curl http://localhost:5000/weather?city=New%20York
```

This should return something like:

```json
{"base":"stations","clouds":{"all":75},"cod":200,"coord":{"lat":40.7143,"lon":-74.006},"dt":1639764799,"id":5128581,"main":{"feels_like":276.05,"humidity":69,"pressure":1016,"temp":278.15,"temp_max":279.82,"temp_min":276.48},"name":"New York","sys":{"country":"US","id":2039034,"sunrise":1639734157,"sunset":1639767679,"type":2},"timezone":-18000,"visibility":10000,"weather":[{"description":"broken clouds","icon":"04n","id":803,"main":"Clouds"}],"wind":{"deg":280,"gust":11.32,"speed":7.72}}
```

## Conclusion

In this blog post, we have seen how to use mach-nix to create reproducible python environments with nix. We have created a simple web application that uses Flask and requests and ran it from our python environment.

Mach-nix is a powerful and easy-to-use tool that can help you manage your python projects and dependencies in a reliable and portable way. You can learn more about mach-nix and its features on its GitHub page: https://github.com/DavHau/mach-nix.

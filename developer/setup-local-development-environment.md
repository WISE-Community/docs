---
title: Setup local development environment
layout: page
date: 2025-11-12 09:00 -0700
---

- [Introduction](#introduction)
- [Requirements](#requirements)
- [Setup](#setup)
- [Additional information](#additional-information)

## Introduction

If you're looking to make changes to WISE, whether it's for the server (WISE-API) or the client (WISE-Client), you've come to the right place!

WISE development uses [Docker](https://www.docker.com/). Docker simplifies and standardizes the development environment so that developers can quickly and easily start developing WISE.

## Requirements

1. Your machine will need at least 10GB of RAM to set up WISE development using Docker.
2. If you are using Windows, It may be easier to use WSL2. Update .wslconfig to allocate at least 10GB of RAM to WSL. ([reference](https://learn.microsoft.com/en-us/windows/wsl/wsl-config))

## Setup

1. Install Docker from [here](https://www.docker.com/products/docker-desktop). In the Docker preferences, set the RAM to at least 6GB.
2. In the same folder, checkout [WISE-Docker-Dev (this project)](https://github.com/WISE-Community/WISE-Docker-Dev), [WISE-API](https://github.com/WISE-Community/WISE-API), and [WISE-Client](https://github.com/WISE-Community/WISE-Client).

```
$ git clone https://github.com/WISE-Community/WISE-Docker-Dev
$ git clone https://github.com/WISE-Community/WISE-API
$ git clone https://github.com/WISE-Community/WISE-Client
$ ls
WISE-Docker-Dev
WISE-API
WISE-Client
```

3. Run `npm install` in the WISE-Client directory

```
$ cd WISE-Client
WISE-Client $ npm install
```

4. Run `docker compose up` in the WISE-Docker-Dev directory

```
WISE-Client $ cd ../WISE-Docker-Dev
WISE-Docker-Dev $ docker compose up
```

5. Wait for everything to download, compile, and start. This can take a while depending on your computer and connection speeds. When you see this in the output, all of the application has started:

```
...
wise-client  | ** Angular Live Development Server is listening on 0.0.0.0:4200, open your browser on http://localhost:4200/ **
wise-client  |
wise-client  |
wise-client  | ✔ Compiled successfully.
```

6. When you see the above output in your log output, WISE will be running at http://localhost:80. Go there with your browser to load the WISE homepage.
7. Log in with admin/pass, or previewuser/wise.

That's it! Any changes that you make to the source code (on both WISE-API and WISE-Client) will be automatically compiled and deployed. Changes to WISE-Client will trigger an automatic reload in the browser.

## Additional information

WISE is open source! The license is GNU General Public License, v3. Please see LICENSE.txt for details. If you would like to contribute code changes like new features or bug fixes, please see the [contributing page](contributing-changes).

To see WISE and curricula developed by the WISE research team at UC Berkeley, please visit [https://wise.berkeley.edu](https://wise.berkeley.edu).

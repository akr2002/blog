---
title: "Install and Use Gitea"
date: 2023-02-12T11:11:29+05:30
lastmod: 2023-02-12T11:11:29+05:30
draft: false 
keywords: [gitea git repository repo]
description: ""
tags: [gitea git]
categories: [linux server]
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
Here I document my steps to get Gitea up and running on a Debian server.
<!--more-->

Gitea is a self-hosted service to host and accept contributions to your git repositories on the web.

## Prerequisites
- git
- curl/wget to obtain the binary
- MySQL/PostgreSQL/MSSQL/SQLite3/TiDB

## Getting Gitea
Go to Gitea's official website for [installation instructions](https://docs.gitea.io/en-us/install-from-binary/). 

It usually boils down to 
1. Obtaining and verifying the binary
2. Creating a user 
3. Creating directory structure
4. Creating a service file
5. Setting up a databse
6. Configuring Gitea

## Create user
Create a user to run Gitea. Here we name it 'gitea' for the sake of our convenience.
```bash
adduer \
--system \
--shell /bin/bash \
--gecos 'Gitea' \
--group \
--disabled-password \
--home /home/gitea \
gitea

```

## Create directory structure
```bash 
mkdir -p /var/lib/gitea/{custom,data.log}
chown -R gitea:gitea /var/lib/gitea
chmod -R 750 /var/lib/gitea
mkdir /etc/gitea
chown root:gitea /etc/gitea
chmod 770 /etc/gitea
```

## Copy Gitea binary to global location
```
cp gitea /usr/local/bin/gitea
```

## Create a service file
Copy the sample [gitea.service](https://github.com/go-gitea/gitea/blob/main/contrib/systemd/gitea.service) to `/etc/systemd/system/gitea.service`, then edit the file with a text editor.

Replace the lines starting with `---` to `+++`.
```
--- #Wants=postgresql.service
--- #After=postgresql.service
+++ Wants=postgresql.service
+++ After=postgresql.service

--- User=git
--- Group=git
+++ User=gitea
+++ Group=gitea 
```

## Set up a database
As user `postgres`, run the following
```
createuser gitea
createdb gitea
psql
alter user gites with encrypted password 'super-secure-password';
grant all privileges on database gitea to gitea;
\q
```

## Set up a web server
Put the following configuration in `/etc/caddy/Caddyfile`
```
gitea.adityakumar.com {
  reverse_proxy localhost:3000

}
```
Now reload caddy
```bash
systemctl reload caddy 
```

## Configure Gitea
Enable and start Gitea
```bash
systemctl enable --now gitea.service 
```

Head to the website, or go to `localhost:3000` if you have physical access to the server. Alternatively you can use port forwarding to configure Gitea using your local machine.

On the page, 
- enter database username, password and database name.
- change Site Title to whatever you prefer. 
- enter `gitea` in Run As Username field.
- change Gitea Base URL to the site's URL

Rest of the options should be left as is unless you have other requirements.

Click on Install Gitea. It should write the configuration to `/etc/gitea/app.ini` and ready for use.

Now change permissions on `/etc/gitea` to make it read-only.
```bash
chmod 750 /etc/gitea
chmod 640 /etc/gitea/app.ini
```


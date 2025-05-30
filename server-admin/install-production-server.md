---
title: Install WISE server
layout: page
date: 2025-05-28 09:00 -0700
---

This page contains the instructions on how to install WISE on your production server. These specific instructions are for installing WISE onto an Ubuntu server, but WISE should work on other types of Linux servers too.

## Update Ubuntu

```
sudo apt update -y
```

```
sudo apt upgrade -y
```

## Install MySQL

```
sudo apt install mysql-server -y
```

Connect to mysql so you can run mysql commands.

```
sudo mysql
```

While in mysql, run these commands. Make sure to replace replace-this-with-a-password-for-the-database and remember the password for later.

```
create database wise_database;
CREATE USER 'wiseproduser'@'%' IDENTIFIED BY 'replace-this-with-a-password-for-the-database';
GRANT ALL PRIVILEGES ON wise_database.* TO 'wiseproduser'@'%';
FLUSH PRIVILEGES;
```

## Install Redis

```
sudo apt install redis-server -y
```

Open the redis.conf file for editing.

```
sudo vim /etc/redis/redis.conf
```

Inside the redis.conf file, change `supervised no` to `supervised systemd`

## Install Nginx

```
sudo apt install nginx -y
```

Run these commands to update /etc/nginx/nginx.conf settings.

```
sed 's/http {/http {\n        add_header ip $server_addr;/' -i /etc/nginx/nginx.conf
sed 's/include \/etc\/nginx\/sites-enabled\/\*;/include \/etc\/nginx\/sites-enabled\/\*;\n\n        ##\n        # Browser preferred language detection \(does NOT require AcceptLanguageModule\)\n        ##\n\n        map \$http_accept_language \$accept_language {\n                ~\*\^tr tr;\n                ~\*\^es es;\n                ~\*\^pt pt;\n                ~\*\^ja ja;\n                ~\*\^zh-Hans zh-Hans;\n                ~\*\^zh-Hant zh-Hant;\n                ~\*\^zh-CN zh-Hans;\n                ~\*\^zh-TW zh-Hant;\n        }/' -i /etc/nginx/nginx.conf
sed 's/gzip on;/gzip on;\n        gzip_types text\/plain text\/xml image\/gif image\/jpeg image\/png image\/svg+xml application\/json application\/javascript application\/x-javascript text\/javascript text\/css;/' -i /etc/nginx/nginx.conf
sed 's/TLSv1 //g' -i /etc/nginx/nginx.conf
sed 's/TLSv1.1 //g' -i /etc/nginx/nginx.conf
```

Delete the default link in /etc/nginx/sites-enabled.

```
rm /etc/nginx/sites-enabled/default
```

Create the file /etc/nginx/sites-enabled/wise.conf and put the contents below into it. Make sure to replace replace-with-website-address with your actual website address. This should be something like wise.berkeley.edu

```
upstream tomcat {
  server 127.0.0.1:8080 fail_timeout=0;
}

server {
  listen 80;
  server_name replace-with-website-address;
  charset utf-8;
  access_log off;
  rewrite ^/wise5/(.*)$ /assets/wise5/$1 last;

  location ~ ^/(curriculum|studentuploads) {
    root /opt/tomcat/webapps;
    try_files $uri $uri/ =404;
  }

  location ~* ^/(admin|api|portal|projectIcons|websocket|teacher\/account\/info|teacher\/management|student\/account\/info|pages) {
    include proxy_params;
    proxy_pass http://tomcat;
    client_max_body_size 50M;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header X-Forwarded-Proto https;
  }

  if ($accept_language ~ "(es|ja|pt|tr|zh-Hant|zh-Hans)") {
    # Redirect "/" to Angular app in browser's preferred language
    rewrite ^/$ /$accept_language permanent;
  }

  # Everything under the Angular app is always redirected to Angular in the correct language
  location ~ ^/(es|ja|pt|tr|zh-Hant|zh-Hans) {
      root /usr/share/nginx/html/wise-client;
      try_files $uri /$1/index.html?$args;
  }

  location / {
    root /usr/share/nginx/html/wise-client/en-US;
    try_files $uri $uri/ /index.html;
  }
}
```

Create a wise-client folder.

```
mkdir /usr/share/nginx/html/wise-client
```

On a web browser, go to the [WISE-Client releases page](https://github.com/WISE-Community/WISE-Client/releases).

Find the release version you want and scroll down to the "Assets" section and right click on the "English build" link (or any other language build) and choose "Copy link address". On your server, use wget to download the build file.

```
wget replace-with-link-to-client-build-file
```

Move the client build file to the wise-client folder. For example if you downloaded the "English Build" which gave you the en-US.tar.gz file, you would do this.

```
mv en-US.tar.gz /usr/share/nginx/html/wise-client
```

Unpack the build file.

```
cd /usr/share/nginx/html/wise-client
tar -xzf en-US.tar.gz
```

Restart Nginx.

```
systemctl restart nginx
```

## Install Java

```
sudo apt install openjdk-11-jdk-headless -y
```

## Install Tomcat

Create Tomcat group.

```
groupadd -g 1001 tomcat
```

Create tomcat user.

```
useradd -u 1001 -g tomcat -c "Apache Tomcat" -d $CATALINA_HOME -s /usr/sbin/nologin tomcat
```

Add ubuntu user to tomcat group.

```
usermod -a -G tomcat ubuntu
```

Create Tomcat directory.

```
mkdir /opt/tomcat
```

Make tomcat user the owner of the tomcat directory.

```
chown tomcat:tomcat /opt/tomcat
```

Download Tomcat 9.

```
wget -P /tmp https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.82/bin/apache-tomcat-9.0.82.tar.gz
```

Unpack Tomcat 9.

```
tar xzvf /tmp/apache-tomcat-9.0.82.tar.gz -C /opt/tomcat --strip-components=1
```

Give tomcat user ownership of the tomcat directory contents.

```
chown -R tomcat:tomcat /opt/tomcat
```

Give tomcat user execute permission on tomcat bin folder.

```
chmod -R u+x /opt/tomcat/bin
```

Create the Tomcat service file. Open the tomcat.service file for editing. Note this file does not exist yet.

```
sudo vim /etc/systemd/system/tomcat.service
```

Paste the text below into the tomcat.service file.

```
[Unit]
Description=Tomcat
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom"
Environment="CATALINA_BASE=/opt/tomcat"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx2048M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```

Remove the default Tomcat ROOT folder.

```
rm -rf /opt/tomcat/webapps/ROOT
```

Download wise.war. On a web browser, go to the [WISE-API releases page](https://github.com/WISE-Community/WISE-API/releases).

Find the release version you want and scroll down to the "Assets" section and right click on the "Build" link and choose "Copy link address". On your server, use wget to download the build file.

```
wget replace-with-link-to-api-build-file
```

Rename wise.war to ROOT.war.

```
mv wise.war ROOT.war
```

Move ROOT.war to the Tomcat webapps folder.

```
mv ROOT.war /opt/tomcat/webapps
```

Add https to the Tomcat server.xml file.

```
sed 's/<Connector port="8080"/<Connector port="8080" scheme="https"/' -i $CATALINA_HOME/conf/server.xml
```

Reload the daemon.

```
systemctl daemon-reload
```

Start Tomcat.

```
systemctl start tomcat
```

Enable Tomcat on startup.

```
systemctl enable tomcat
```

Create Tomcat curriculum and studentuploads folders.

```
sudo -u tomcat -g tomcat mkdir /opt/tomcat/webapps/curriculum
sudo -u tomcat -g tomcat mkdir /opt/tomcat/webapps/studentuploads
```

Create application.properties.

```
cp /opt/tomcat/webapps/ROOT/WEB-INF/classes/application_sample.properties /opt/tomcat/webapps/ROOT/WEB-INF/classes/application.properties
```

Look through application.properites and set/update values appropriately. In particular make sure you update the values below.

- wise.hostname (this should be something like https\://wise.berkeley.edu)
- wise4.hostname (this should be something like https\://wise.berkeley.edu/legacy)
- curriculum_base_dir (this should be /opt/tomcat/webapps/curriculum)
- project_icons_base_dir (this should be /opt/tomcat/webapps/ROOT/projectIcons)
- studentuploads_base_dir (this should be /opt/tomcat/webapps/studentuploads)
- spring.datasource.password (this should be the replace-this-with-a-password-for-the-database you created earlier)

If you want to receive emails when users fill out the contact form you will need to set these values

- spring.mail.host
- spring.mail.port
- spring.mail.username
- spring.mail.password
- contact_email

If you want to allow users to log in with their Google account you will need to set these values

- google.clientId
- google.clientSecret
- google.tokens.dir

Restart Tomcat.

```
systemctl restart tomcat
```

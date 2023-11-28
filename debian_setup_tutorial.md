# Tutorial: Setting Up a Debian 12 Server with Nginx on DigitalOcean

## Introduction

This tutorial will guide you through setting up a new Debian 12 server on DigitalOcean. You will create a new user with administrative privileges, disable SSH access for the root user, install Nginx, and configure it to serve a sample website.

## Prerequisites

- A DigitalOcean account
- A fresh Debian 12 server instance

## Step 1: Accessing Your Server

Access your server ussing SSH as the root user. Replace 'your_server_ip' with your server's IP address.
```bash
ssh root@your_server_ip
```

## Step 2: Creating a New User

Create a new user, Let's say the name of the new user is 'lsy42'.

```bash
adduser -ms /bin/bash lsy42
```
or 
```bash
adduser lsy42
```

Set and confirm the new user's password at the prompt.

```bash
passwd lsy42
```

## Step 3: Granting Administrative Privileges

If password is updated successfully,
Give your new user sudo privileges to perform administrative tasks.

```bash
usermod -aG sudo lsy42
```

## Step 4: Setting Bash as the Login Shell - step 2 overlapped?

Ensure that bash is the default shell for the new user.

```bash
chsh -s /bin/bash lsy42
```
## Step 5: Enabling SSH Access for the New User

Copy the SSH keys from the root user to the new user's account

```bash
cp -r .ssh/home/lsy42
chown -R lsy42:lsy42 /home/lsy42/.ssh
```

or with Step 6,

```bash
rsync --archive --chown=exampleuser:exampleuser ~/.ssh /home/exampleuser
```

## Step 6: Disabling SSH Root Login

Edit the SSH configuration file with a text editor like vim.

```bash
vim /etc/ssh/sshd_config
```

Find the line that says **'PermitRootLogin yes'** and change it to **'PermitRootLogin no'**. Save and close the file. Restart the SSH service to apply the changes.

```bash
systemctl restart sshd
```

## Step 7: Installing Nginx

Update your package lists and install Nginx.

```bash
sudo apt update
sudo apt install nginx
```

## Step 8: Configuring Nginx to Serve a Sample Website

Create a directory for your website.

```bash
sudo mkdir -p /var/www/my-site
```

Assign ownership of the directory to your user.

```bash
sudo chown -R lsy42:lsy42 /var/www/mywebsite
```

Create a sample **'index.html'** file in that directory.

```bash
sudo vim index.html
```

Create a new Nginx server block configuration file.

```bash
sudo vim /etc/nginx/sites-available/my-site
```

Paste the following configuration. Replace **your_server_ip** with your actual server IP. Save and exit the file. 

```bash
server {
    listen 80;
    server_name your_server_ip;

    root /var/www/my-site;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
Enable the file by creating a symbolic link to the **sites-enabled** directory.

```bash
sudo ln -s /etc/nginx/sites-available/my-site /etc/nginx/sites-enabled/
```

Test your Nginx configuration for errors.

```bash
sudo nginx -t
```

Restart Nginx to apply the changes.

```bash
sudo systemctl restart nginx
```

## Conclusion
You now have a Debian 12 server running Nginx, serving a simple web page, and a secure user for SSH access.


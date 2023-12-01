# Tutorial: Setting Up a Debian 12 Server with Nginx on DigitalOcean

## Introduction

This tutorial will guide you through setting up a new Debian 12 server on DigitalOcean. You will create a new user with administrative privileges, disable SSH access for the root user, install Nginx, and configure it to serve a sample website.

## Prerequisites

- A DigitalOcean account
- A fresh Debian 12 server instance

## Step 1: Create a Debian 12 Server on DigitalOcean

1. Log in to your DigitalOcean account.

2. Click on the "Create Droplet" button.

3. Choose the following options:
   - Distribution: Debian 12 (Choose the latest available version)
   - Plan: Choose your preferred plan.
   - Data Center Region: Select a data center region closest to your target audience.
   - Authentication: Choose "SSH Key" and add your SSH public key if you have one, or you can use a password for now.

4. Click on the "Create Droplet" button.

5. Wait for the server to be created. DigitalOcean will provide you with the server's IP address.

## Step 2: Connect to Your Server via SSH

Open your terminal and use SSH to connect to your server. Replace `your_ip_address` with your server's actual IP address. This will allow you to access your server as the root user.


```bash
ssh root@your_ip_address
```


## Step 3: Creating a New User

Create a new user, Let's say the name of the new user is 'lsy42'. 
Ensure that bash is the default shell (Setting bash as the log in shell) for the new user using *-ms /bin/bash*.

```bash
adduser -ms /bin/bash lsy42
```

Set and confirm the new user's password at the prompt.

```bash
passwd lsy42
```

## Step 4: Granting Administrative Privileges

If password is updated successfully, we will add the new user to the sudo group to grant administrative privileges.

Give your new user sudo privileges to perform administrative tasks.

```bash
usermod -aG sudo lsy42
```

## Step 5: Enabling SSH Access for the New User

Copy the SSH keys from the root user to the new user's account

```bash
cp -r .ssh/home/lsy42
chown -R lsy42:lsy42 /home/lsy42/.ssh
```


## Step 6: Configure SSH - Disabling SSH Root Login

Edit the SSH configuration file with a text editor like vim.

```bash
sudo vim /etc/ssh/sshd_config
```

Find the line that says **'PermitRootLogin yes'** and change it to **'PermitRootLogin no'**. Save and close the file. Restart the SSH service to apply the changes.

```bash
sudo systemctl restart sshd
```

## Step 7: Installing Nginx

Update your package lists and install Nginx.

```bash
sudo apt update
sudo apt install nginx
```

## Step 8: Configuring Nginx to Serve a Sample Website

Create a directory for your website. Let's say your website name is 'my-site'

```bash
sudo mkdir -p /var/www/my-site
```

Assign ownership of the directory to your user.

```bash
sudo chown -R lsy42:lsy42 /var/www/my-site
```

Create a sample **'index.html'** file in that directory.
Save the file and exit the text editor.

```bash
sudo vim index.html
```

Configure Nginx to serve your sample website by creating a new virtual host configuration file:

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

## Step 9: Access Your Sample Website

Open a web browser and enter your server's IP address or domain name. You should see your sample website served by Nginx.


## Conclusion

Congratulations! You have successfully set up a Debian 12 server on DigitalOcean, created a new regular user with administrative privileges, prevented the root user from SSH access, installed Nginx, and configured it to serve a sample website.


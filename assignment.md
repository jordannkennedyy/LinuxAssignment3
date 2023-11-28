This tutorial acts as a guide for configuring an Nginx instance to serve a sample website. 

To achieve the end goal, this tutorial will guide you through the the following steps:

Starting from a Fresh Debian 12 server on digitalocean
    Create a new regular user:
        User has bash as login shell
        User can perform administrative tasks
        User can access the server via SSH

Prevent the root user from connecting to the server via SSH
Install nginx
Configure nginx to serve a sample website



1. Creating a new regular user:
    Use the command below to create a new user. 

```bash
useradd -ms /bin/bash <user-name>
```

2. Next, lets add a password for the new user:
```bash
passwd <user-name>
```

3. In order for the new user to complete configuration tasks, lets give the user admin abilities:
```bash
sudo usermod -aG sudo james
```

4. For security purposes, it it best practice to disable root login to the remote server. To do that (While logged-in as Root):
```bash

# copy .ssh from root home directory to /home/<username>
cp -r .ssh /home/james

# after .ssh has been copied to the users home directory, change the ownership so that the user now owns it and all files contained within (both the user and the user group) 
sudo chown james:james -R .ssh

# Next, cd into the root /etc/ssh
cd /etc/ssh

# open sshd_config in vim
sudo vim sshd_config

# find permitRootLogin and change to no
PermitRootLogin no
```

6. Logout, then log in to your new user using the username and IP address of your server
```bash
    <username>@<ip-address>
```

5. Now that we're logged-in as the ```<username>```, lets install nginx
```bash
sudo apt install nginx
```

6. Lets create a directory to hold our sample website. To serve documents:
```bash
# cd into /var/www
cd /var/www

# create a new directory called hello-world
sudo mkdir hello-world

# create a new index.html file
sudo vim index.html

# add your html code to this file
"add html code"
```


7. We will now configure our server block. To do so:
```bash
# cd into sites-available
cd /etc/nginx/sites-available

# create a hello-world configuration file
sudo vim hello-world.conf
```

8. Copy paste the following into hello-world.conf
```bash

server {
	listen 80 default_server;
	listen [::]:80 default_server;
	
	root /var/www/html;
	
	index index.html index.htm index.nginx-debian.html;
	
	server_name _;
	
	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}
}

```

9. Configure .conf
In the code above, change root /var/www/html to /var/www/hello-world
```bash
server {
	listen 80 default_server;
	listen [::]:80 default_server;
	
    #this is what we are changing
	root /var/www/hello-world;
	
	index index.html index.htm index.nginx-debian.html;
	
	server_name _;
	
	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}
}
```

10. Create symbolic link, must be in /etc/nginx/sites-available
```bash

sudo ln -s /etc/nginx/sites-available/hello-world.conf /etc/nginx/sites-enabled

```

11. Remove default from /etc/nginx/sites-enabled
```bash

cd /etc.nginx/sites-enabled

sudo unlink default

```

12. Check to see if the symbolic is working.
```bash

sudo nginx -t
# If no errors, the link is working correctly
```

13. Lastly, restart the nginx service
```bash

sudo systemctl restart nginx.service

# run curl + your IP to ensure the correct html code is appearing

curl <IP>

```

14. And thats it! Congratulations on creating your first sample website on Nginx


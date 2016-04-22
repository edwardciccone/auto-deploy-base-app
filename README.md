# Ruby on Rails Automatic Deployment Starter App

## Step 1 - Configuring Server

### Create a AWS Ubuntu EC2 instance 
Use default settings for everything except when configuring the Security Group. Add a new role with the following settings:  
<strong>Type: HTTP, Protocol: TCP, Port Range: 80, Source: Anywhere 0.0.0.0/0</strong>  

### Login To Server As Root  
<code>$ ssh -i "your-pem-file.pem" ubuntu@your-ec2-instance.compute-1.amazonaws.com</code>  
<code>$ sudo -s</code>  

### Setting Up The Environment  
<code>$ apt-get update</code>  
<code>$ apt-get install build-essential</code>  
<code>$ wget -O ruby-install-0.5.0.tar.gz https://github.com/postmodern/ruby-install/archive/v0.5.0.tar.gz</code>  
<code>$ tar -xzvf ruby-install-0.5.0.tar.gz</code>  
<code>$ cd ruby-install-0.5.0/</code>  
<code>$ make install</code>  
<code>$ ruby-install --system ruby 2.2.2 -- --disable-install-rdoc</code>  
<code>$ rm -r ~/ruby-install-*</code>  
<code>$ gem install bundler</code>  

## Step 2 - Configuring The User 

### Create A New User  
<code>$ useradd -d /home/your-new-user -m -s /bin/bash your-new-user</code>  

### Login As User  
On your personal computer copy your public key (<code>~/your/ssh/path/id_rsa.pub</code>).  
Then on the server paste the <code>id_rsa.pub</code> with the following:  
<code>$ mkdir -p /home/your-new-user/.ssh</code>  
<code>$ touch /home/your-new-user/.ssh/authorized_keys</code>  
  
Open the new file and paste your public key:  
<code>$ nano /home/your-new-user/.ssh/authorized_keys</code>  
  
Then run the following:  
<code>$ chown -R your-new-user /home/your-new-user/.ssh</code>  
<code>$ chmod 600 /home/your-new-user/.ssh/authorized_keys</code>  
  
Now login as the new user:  
<code>$ ssh your-new-user@your-ec2-instance.compute-1.amazonaws.com</code>  

If it fails try:
<code>$ SSH_AUTH_SOCK=0 ssh your-new-user@your-ec2-insance.compute-1.amazonaws.com</code>  
Then you can run it with out <code>SSH_AUTH_SOCK=0</code>.  

Check your ruby version:  
<code>$ ruby -v</code>  
  
Generate your Github deploy key:  
<code>$ ssh-keygen -t rsa</code>  
  
Copy the key (<code>cat ~/.ssh/id_rsa.pub</code>) to your Github Repos Deploy Keys under Settings.  

## Step 3 - Further Server Configuration  
  
### Install Git  

Login as root from your personal computer:  
<code>$ ssh -i "your-pem-file.pem" ubuntu@your-ec2-instance.compute-1.amazonaws.com</code>  
<code>$ sudo -s</code>  

Then install Git:  
<code>$ apt-get install git-core</code>  
  
### Capistrano Configuration  

In <code>config/deploy.rb</code> set your application name and Github Repo:  
<code>set :application, 'your-app-name'</code>  
<code>set :repo_url, 'git@github.com:your-github-user/your-github-repo.git'</code>  
  
Then set where to deploy to:  
<code>set :deploy_to, '/home/your-new-user/app'</code>  

In <code>config/deploy/production.rb</code> insert:  
<code>server 'your-ec2-instance.compute-1.amazonaws.com', user: 'your-new-user', roles: %w{app db web}</code>  
  
### Database Configuration  
  
Login as root again and install SQLite3's development headers:  
<code>$ apt-get install libsqlite3-dev</code>  

### JavaScript Runtime Configuration  
Still logged in as root run:  
<code>$ apt-get install nodejs</code>  

### Deploying the first version  
On your personal computer run:  
<code>$ bundle exec cap production deploy</code>  

This will create a starter structure on your Server.  
  
Again on your personal computer generate the Production Key Secret:  
<code>$ rake secret</code>  
  
Copy the secret and as Root on the Server run:    
<code>$ touch /home/your-new-user/app/shared/config/secrets.yml</code>  
<code>$ nano /home/your-new-user/app/shared/config/secrets.yml</code>  

In your <code>secrets.yml</code> file insert:  
<code>production:</code>    
<code>  secret_key_base: "your-production-secret-key"</code>  
  
On your personal computer rerun the deploy command:   
<code>$ bundle exec cap production deploy</code>  
  
This finishes the file structure on the server (ls <code>/home/your-new-user/app</code>).  
  
## Step 4 - Install Nginx and Passenger  
  
### Install Curl Development Headers With SSL Support  
  
As root run: 
<code>$ apt-get install libcurl4-openssl-dev</code>  

### Passenger  

Then install passenger:  
<code>$ gem install passenger</code>  
  
### Nginx  

After installing Passenger run:  
<code>$ passenger-install-nginx-module</code>  

### Configuring Nginx  
  
In your nginx config file (<code>/opt/nginx/conf/nginx.conf</code>) put:  
<code>user  your-new-user;</code>  
  
at the top of the file. And then put:  

<code>server { </code>  
<code>  listen 80;</code>  
<code>  server_name your-ec2-instance.compute-1.amazonaws.com;</code>  
<code>  root /home/your-new-user/app/current/public;</code>  
<code>  passenger_enabled on;</code>  
<code>}</code>  

Then start Nginx with:  
<code>$ /opt/nginx/sbin/nginx</code>  
  
Now check <code>http://your-ec2-instance.compute-1.amazonaws.com/</code>  

## Step 5 - Configuring The Shared Directory  

### Setting Up The Linked Files  
  
In <code>config/deploy.rb</code> set the following:  
<code>set :linked_files, fetch(:linked_files, []).push('config/database.yml', 'config/secrets.yml')</code>  

Then create the linked database directory:  
<code>$ touch /home/your-new-user/app/shared/config/database.yml</code>

Edit the new database file:    
<code>$ nano /home/your-new-user/app/shared/config/database.yml</code>  

These settings can be found on your personal computer in the <code>config/database.yml</code> file  

<code>production: </code>  
<code>  adapter: sqlite3</code>  
<code>  pool: 5</code>  
<code>  timeout: 5000</code>  
<code>  database: db/production.sqlite3</code>  
  
## Your Done!  
<strong>
When you make changes remember to commit and push your changes, and then rerun <code>bundle exec cap production deploy</code>.  
To test your changes locally use <code>bundle exec passenger start</code> on your personal computer.  
</strong>
 


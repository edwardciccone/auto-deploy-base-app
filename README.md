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

## Step 2 - Deploying  

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
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  

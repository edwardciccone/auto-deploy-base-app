# Ruby on Rails Automatic Deployment Starter App

## Step 1

### Create a AWS Ubuntu EC2 instance 
Use default settings for everything except when configuring the Security Group. Add a new role with the following settings:  
<strong>Type: HTTP, Protocol: TCP, Port Range: 80, Source: Anywhere 0.0.0.0/0</strong>  

### Login To Server As Root  
<code>$ ssh -i "your-pem-file.pem" ubuntu@your-ec2-instance.computer-1.amazonaws.com</code>  
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
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  
<code>$ </code>  

# Multi-variant machine

Vagrant is able to define and control multiple guest machines per Vagrantfile. This is known as a "multi-machine" environment.

# Provisioning and tests

This exercise utilised vagrant and VirtualBox to make sure the provision file passed all the tests.

#### Installation

##### Dependencies:
- install Vagrant
- install VirtualBox
- install Ruby
- install Bundler

### Method 1:
To run the file:
1. clone the repo
2. make sure you are on the root of the project and can see the Vagrant file
3. then run:
```bash
vagrant up
```
### Method 2: 
Otherwise follow steps below:    
1. Update vagrant
```
# Install required plugins
required_plugins = ["vagrant-hostsupdater"]
required_plugins.each do |plugin|
    exec "vagrant plugin install #{plugin}" unless Vagrant.has_plugin? plugin
end

Vagrant.configure("2") do |config|
  # configures the machine app
  config.vm.define "app" do |app|
    app.vm.box = "ubuntu/xenial64"
    app.vm.network "private_network", ip: "192.168.10.100"
    app.hostsupdater.aliases = ["development.local"]
    app.vm.synced_folder ".", "/home/vagrant/app"
    app.vm.provision "shell", path: "environment/app/provision.sh", privileged: false
  end

  config.vm.define "db" do |db|
    db.vm.box = "ubuntu/xenial64"
    db.vm.network "private_network", ip: "192.168.10.150"
    db.vm.provision "shell", path: "environment/db/provision.sh", privileged: false
  end
end

``` 

2. Path into environment with "cd 'dir'" or 'cd ..' to go back 

3. Once in the folder 'mkdir db'  to make db/ dir, make a provision.sh shell file using 'touch provsion.sh' 
in both app/ and db/ dir in the environment dir 

4. Starting on line 1, not 0 of the provision.sh in app/ 
```
#!/bin/bash

# Update the sources list
sudo apt-get update -y

# upgrade any packages available
sudo apt-get upgrade -y

# install nginx
sudo apt-get install nginx -y

# install git
sudo apt-get install git -y

# install nodejs
sudo apt-get install python-software-properties
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install nodejs -y

# install pm2
sudo npm install pm2 -g

#install MongoDB
echo 'export DB_HOST=mongodb://192.168.10.150:27017/posts' >> ~/.bashrc
sudo npm install

```

Starting on line1, not of the provision.sh in db/ 
    
```

wget -qO - https://www.mongodb.org/static/pgp/server-3.2.asc | sudo apt-key add -

echo "deb http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" | sudo tee /etc>
sudo apt-get update -y

sudo apt-get install -y mongodb-org=3.2.20 mongodb-org-server=3.2.20 mongodb-org-shell=3.2.20 m>

sudo sed -i "s,\\(^[[:blank:]]*bindIp:\\) .*,\\1 0.0.0.0," /etc/mongod.conf
sudo systemctl start mongod
sudo systemctl status mongod
sudo systemctl enable mongod

```

5. Navigate to ~/"file_name"/starter_code/tests
run 

```
rake spec

```
There should be zero failures 

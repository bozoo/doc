CHEF
====

Install Chef Server
-------------------

1. Chef server's hostname need to be FQDN

    rpm -ivh chef-server-11.0.8-1.el6.x86_64.rpm chef-11.4.4-2.el6.x86_64.rpm
    chef-server-ctl reconfigure
    chef-server-ctl test

2. Configure admin account

    firefox https://$serverChef

3. Copy admin pubkey and private key


4. Install Chef client

    rpm -ivh chef-11.4.4-2.el6.x86_64.rpm
    chef-client -v

5. Install Git

    yum install git
    git clone git://github.com/opscode/chef-repo.git

6. Configure Chef Server

    mkdir -p ~/chef-repo/.chef
    knife configure --initial
    cp .chef/knife.rb chef-repo/.chef/
    cp /etc/chef-server/chef-validator.pem chef-repo/.chef/
    cp /etc/chef-server/admin.pem chef-repo/.chef/
    cp chef-repo/.chef/chef-validator.pem /etc/chef-server/validation.pem
    echo 'export PATH="/opt/chef/embedded/bin:$PATH"' >> ~/.bash_profile && source ~/.bash_profile
    knife client list

@http://docs.opscode.com/install_bootstrap.html

CHEF INSTALL ON RASPBERRY PI
----------------------------

    apt-get update && apt-get upgrade
    apt-get install ruby1.9.1 ruby1.9.1-dev build-essential wget

    gem update --no-rdoc --no-ri
    gem install ohai --no-rdoc --no-ri --verbose
    gem install chef --no-rdoc --no-ri --verbose

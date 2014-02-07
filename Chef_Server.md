CHEF SERVER
===========

Administration
--------------

/opt/chef-server – The installation from the package is here.
/etc/chef-server – The API specific configuration files and keys are here.
/var/opt/chef-server – The dependent services such as RabbitMQ, Nginx and so on are here, including the PostgreSQL database, SOLR indexes.
/var/log/chef-server – All the runit services write their output here.

    chef-server-ctl

reconfigure : configure chef
test : test chef
status : show status of all chef services
start : start chef
stop : stop chef
tail : tail logs
cleanse : remove all configuration

Reconfigure use /etc/chef-server/chef-server-running.json

Install Chef Server
-------------------

Chef server's hostname need to be FQDN

    rpm -ivh chef-server-11.0.8-1.el6.x86_64.rpm chef-11.4.4-2.el6.x86_64.rpm
    chef-server-ctl reconfigure
    chef-server-ctl test

Configure admin account

    firefox https://$serverChef

Copy admin pubkey and private key


Install Chef client

    rpm -ivh chef-11.4.4-2.el6.x86_64.rpm
    chef-client -v

Install Git

    yum install git
    git clone git://github.com/opscode/chef-repo.git

Configure Chef Server

    mkdir -p ~/chef-repo/.chef
    knife configure --initial
    cp .chef/knife.rb chef-repo/.chef/
    cp /etc/chef-server/chef-validator.pem chef-repo/.chef/
    cp /etc/chef-server/admin.pem chef-repo/.chef/
    cp chef-repo/.chef/chef-validator.pem /etc/chef-server/validation.pem
    echo 'export PATH="/opt/chef/embedded/bin:$PATH"' >> ~/.bash_profile && source ~/.bash_profile
    knife client list

@http://docs.opscode.com/install_bootstrap.html





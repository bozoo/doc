CHEF SOLO HOW TO
================

Create Chef kitchen folder:

    mkdir chef-solo-example && cd chef-solo-example

Edit Gemfile to install gems with [Bundler](http://bundler.io/):

```ruby
source:rubygems
gem 'knife-solo'
gem 'librarian'
```

    bundle

Install Vagrant and VirtualBox

Create a kitchen with knife:

    knife solo init .
    # for version < 0.1.0 you should use "knife kitchen ."
    ls -la
    total 48drwxr-xr-x  14 leo  staff   476 Jan  4 19:01 .
    drwxr-xr-x  69 leo  staff  2346 Jan  4 18:43 ..
    drwxr-xr-x  13 leo  staff   442 Jan  4 18:57 .git
    -rw-r--r--@  1 leo  staff    38 Jan  4 18:57 .gitignore
    -rw-r--r--@  1 leo  staff     9 Jan  4 18:51 .rvmrc
    -rw-r--r--@  1 leo  staff    98 Jan  4 18:53 Gemfile
    -rw-r--r--   1 leo  staff  2033 Jan  4 18:53 Gemfile.lock
    -rw-r--r--@  1 leo  staff    19 Jan  4 18:56 README.md
    drwxr-xr-x   3 leo  staff   102 Jan  4 19:01 cookbooks
    drwxr-xr-x   3 leo  staff   102 Jan  4 19:01 data_bags
    drwxr-xr-x   3 leo  staff   102 Jan  4 19:01 nodes
    drwxr-xr-x   3 leo  staff   102 Jan  4 19:01 roles
    drwxr-xr-x   3 leo  staff   102 Jan  4 19:01 site-cookbooks
    -rw-r--r--   1 leo  staff   319 Jan  4 19:01 solo.rb

Command init (kitchen) is used to create a new directory structure that fits with chef’s standard structure and can be used to build and store recipes:

 - cookbooks: directory for Chef cookbooks. This directory will be used for vendor cookbooks
 - data_bags: directory for Chef Data Bags
 - nodes: directory for Chef nodes
 - roles: directory for Chef roles
 - site-cookbooks: directory for your custom Chef cookbooks
 - solo.rb: file used by Chef Solo with configuration settings

Create librarian Cheffile to manage the cookbooks:

    librarian-chef init
    create  Cheffile

Add to Cheffile nginx cookbook. More cookbooks you can find at [Opscode Community](http://community.opscode.com).

Edit Cheffile:

```ruby
#!/usr/bin/env ruby
#^syntax detection
site 'http://community.opscode.com/api/v1'
cookbook 'runit'
cookbook 'nginx',:git => 'git://github.com/opscode-cookbooks/nginx.git'
```

    librarian-chef install

Create a node file. Chef node file always have name as server host.
To create this file automatically and check, what Chef Solo installed on server:
You can use knife command “prepare”. This command installs Ruby, RubyGems and Chef on a given host.
It’s structured to auto-detect the target OS and change the installation process accordingly:

    knife solo prepare host_username@host
    # for version < 0.1.0 you should use "knife prepare host_username@host"

This command apply the same parameters as ssh command. Fox example, executing with ssh key:

    knife solo prepare -i key/ssh_key.pem host_username@host

Create node file vagrant.json:

```json
{
  "nginx": {
    "version": "1.2.3",
    "default_site_enabled": true,
    "source": {
      "modules": ["http_gzip_static_module", "http_ssl_module"]
    }
  },
  "run_list": [
    "recipe[nginx::source]"
  ]
}
```

Download vagrant box

    vagrant box add precise64 http://dl.dropbox.com/u/1537815/precise64.box
    vagrant init precise64

Edit Vagrantfile to define chef solo:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby:
Vagrant::Config.run do |config|
  config.vm.box = "precise64"
  VAGRANT_JSON = JSON.parse(Pathname(__FILE__).dirname.join('nodes', 'vagrant.json').read)
  config.vm.provision:chef_solo do |chef|
    chef.cookbooks_path = ["site-cookbooks", "cookbooks"]
    chef.roles_path = "roles"
    chef.data_bags_path = "data_bags"
    chef.provisioning_path = "/tmp/vagrant-chef"

    # You may also specify custom JSON attributes:
    chef.run_list = VAGRANT_JSON.delete('run_list')
    chef.json = VAGRANT_JSON
    # old way
    #VAGRANT_JSON['run_list'].each do |recipe|
      # chef.add_recipe(recipe)
    #end if VAGRANT_JSON['run_list']
  end
end
```

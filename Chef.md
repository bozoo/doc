CHEF
====

Apply a recipe outside of cookbooks: use [chef-apply](https://gist.github.com/jtimberman/4366061)

Use Chef in interactive mode (Shell): use shef command (only in Chef 11)

Configure Nodes
---------------

Add node and install chef-client on it (need SSH between workstation and node and Internet access to download chef-client)

    knife bootstrap server.example.com -V

Install Chef client on Raspbian
--------------------------------

    apt-get update && apt-get upgrade
    apt-get install ruby1.9.1 ruby1.9.1-dev build-essential wget

    gem update --no-rdoc --no-ri
    gem install ohai --no-rdoc --no-ri --verbose
    gem install chef --no-rdoc --no-ri --verbose

Use nodes Tags
--------------

View the tags of a node:

    knife tag list www.example.com
    decommissioned

Add a tag to a node:

    knife tag create www.example.com powered_off
    Created tags powered_off for node www.example.com.

Remove a tag from a node:

    knife tag delete www.example.com powered_off
    Deleted tags powered_off for node www.example.com.


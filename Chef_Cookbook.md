CHEF COOKBOOK
=============

Edit lines in a file
--------------------

The best practice is to manage the whole file with a template.
But if really need to edit lines in existing files, we must use the library [Chef::Util::FileEdit](http://rubydoc.info/gems/chef/10.16.2/Chef/Util/FileEdit) with a ruby_block

Example:

```ruby
ruby_block "edit etc hosts" do
  block do
    rc = Chef::Util::FileEdit.new("/etc/hosts")
    rc.search_file_replace_line(
      /^127\.0\.0\.1 localhost$/,
      "127.0.0.1 #{new_fqdn} #{new_hostname} localhost"
    )
    rc.write_file
  end
end
```

The community [line](http://community.opscode.com/cookbooks/line) allow to use resources which implement this Library.

Extending the Recipe DSL with helpers
-------------------------------------

Create a cookbook dedicated to custom resources:

    knife cookbook create my_helpers

Then create the library file:

    touch cookbooks/my_helpers/libraries/default.rb

Then, add this method to its class, Chef::Recipe:

```ruby
class Chef
  class Recipe
    def chef_version
      node['chef_packages']['chef']['version']
    end
  end
end
```

To use this in a recipe, simply call that method.

Example:

```ruby
mac_service_supported = version_checker.include?(chef_version)
```

Example 2:

Helper library the Encrypted Data Bag example.

Edit cookbooks/my_helpers/libraries/encrypted_data_bag_item.rb:

```ruby
class Chef
  class Recipe
    def encrypted_data_bag_item(bag, item, secret_file = Chef::EncryptedDataBagItem::DEFAULT_SECRET_FILE)
      DataBag.validate_name!(bag.to_s)
      DataBagItem.validate_id!(item)
      secret = EncryptedDataBagItem.load_secret(secret_file)
      EncryptedDataBagItem.load(bag, item, secret)
    rescue Exception
      Log.error("Failed to load data bag item: #{bag.inspect} #{item.inspect}")
      raise
    end
  end
end
```

To use it on a recipe:

```ruby
user_creds = encrypted_data_bag_item("secrets", "credentials)
```

Use Node Tags
-------------

Search nodes with a tag:

```ruby
decommissioned_nodes = search(:node, "tags:decommissioned")
```

Use tagged? to see if the node running Chef has a specific tag:

```ruby
if tagged?("decommissioned")
  raise "Why am I running Chef if I'm decommissioned?"
end
```

If the tags of the node need to be modified during a run, that can be done with the tag and untag methods.

```ruby
tag("deployed")
log "I'm printed if the tag deployed is set." do
  only_if { tagged?("deployed") }
end
```

Untag a node after migration:

```ruby
if tagged?("run_migrations")
  execute "rake db:migrate" do
    cwd "/srv/myapp/current"
    notifies:create, "ruby_block[untag-run-migrations]",:immediately
  end
end

ruby_block "untag-run-migrations" do
  block do
    untag("run_migrations")
  end
  only_if { tagged?("run_migrations") }
end
```

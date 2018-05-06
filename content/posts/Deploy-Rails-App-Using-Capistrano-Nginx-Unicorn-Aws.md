---
title: Deploying Rails App to AWS using Capistrano (Unicorn, Nginx, RVM, )
description: "How to deploy a rails app to AWS using Capistrano script, with Unicor, Nginx server and RVM"
date: 2018-05-05
lastmod: 2018-05-05
tags: [Capistrano, Rails, AWS, RVM, nginx, unicorn]

---
### Introduction
In this post I am going to show you how to setup capistrano so that you can deploy your rails app to AWS. I will assume that you have an EC2 instance in AWS and you already are have a rails app with Nginx and Unicorn. With that assumption I will you how to setup capistrano so that your deployments are super easy.


Add the following Gems to your Gemfile. If you are using rvm for managing ruby version, you need to include 'capistrano-rvm' and NOT 'rvm-capistrano' I have seen some blogs/articles using 'rvm-capistrano', but it din't work for me. I recommend 'capistrano-rvm' 
```ruby
#Gemfile
group :development do
    gem 'capistrano'
    gem 'capistrano-rails'
    gem 'capistrano-rvm'
    gem 'capistrano-bundler'
    gem 'capistrano3-unicorn'
end
```

Then install the gems. Run the following command in the shell console.
```shell
$bundle install
```

```shell
$bundle exec cap install
# Files below will be created 
mkdir -p config/deploy
create config/deploy.rb
create config/deploy/staging.rb
create config/deploy/production.rb
mkdir -p lib/capistrano/tasks
create Capfile
Capified
$
```

Edit Capfile.
 If you are using rvm for managing ruby versions, you need to include, require 'capistrano/rvm' and add the line  set :rvm_ruby_string, '2.4.1', to let capistrano which version to switch to. Replace '2.4.1' to your ruby version that you are using in the ec2 server. 

```ruby
#Capfile
# Load DSL and set up stages
require "capistrano/setup"
# Include default deployment tasks
require "capistrano/deploy"

require "capistrano/rails"
require "capistrano/rails/assets"
require "capistrano/rails/migrations"
require 'capistrano/rvm'
require "capistrano/bundler"
require "capistrano3/unicorn"

set :rvm_ruby_string, '2.4.1' 
set :linked_files, %w{config/secrets.yml config/database.yml}
Dir.glob('lib/capistrano/tasks/*.rake').each { |r| import r }
```

Edit deploy.rb. The default_environment is for RVM again. You specify the name of the application, the git url and the branch of the code. 
```ruby
#config/deploy.rb
# config valid for current version and patch releases of Capistrano
lock "~> 3.10.2"
set :default_environment, { 
  'PATH' => "/home/ec2-user/.rvm/gems/ruby-2.4.1/bin:/home/ec2-user/.rvm/gems/ruby-2.4.1@global/bin:/home/ec2-user/.rvm/rubies/ruby-2.4.1/bin:/home/ec2-user/.rvm/bin:/home/ec2-user/.nvm/versions/node/v6.11.5/bin:/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/opt/aws/bin:/home/ec2-user/.local/bin:/home/ec2-user/bin:$PATH",
  'RUBY_VERSION' => 'ruby 2.4.1',
  'GEM_HOME' => '/home/ec2-user/.rvm/gems/ruby-2.4.1',
  'GEM_PATH' => '/home/ec2-user/.rvm/gems/ruby-2.4.1' 
}

set :application, "contactbook"
set :repo_url, "git@github.com:hhemanth/contactbook.git"
set :branch, "master"

set :deploy_to, '/home/ec2-user/contactbook'
set :log_level, :debug
 
set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system public/assets}
 
namespace :deploy do
 desc 'Restart application'
 task :restart do
 invoke 'unicorn:restart'
 end
end
after 'deploy:publishing', 'deploy:restart'
```
  
Edit staging.rb. Replace "ec2–111111.compute-1.amazonaws.com" with the actual ec2 url. 
```ruby
# config/deploy/staging.rb
set :staging, :master
set :unicorn_rack_env, "staging"
set :rails_env, 'staging'
set :migration_role, 'db'
set :branch, "master"

role :app, %w{ec2–111111.compute-1.amazonaws.com} # Server IP Address
role :web, %w{ec2–111111.compute-1.amazonaws.com}
role :db, %w{ec2–111111.compute-1.amazonaws.com}
 
set :ssh_options, {
keys: [File.expand_path('~/keys/hhemanth.pem')],
forward_agent: true,
auth_methods: %w(publickey)
}
 
server 'ec2–111111.compute-1.amazonaws.com', user: 'ec2-user', roles: %w{web app db}
```

Then you need to add config/unicorn/staging.rb. This is the unicorn config
```ruby
root = "/home/ec2-user/contactbook/current"

pid           "#{root}/tmp/pids/unicorn.pid"
stderr_path   "#{root}/log/unicorn_error.log"
stdout_path   "#{root}/log/unicorn.log"

listen "/tmp/unicorn.socket", :backlog => 2048

preload_app true
working_directory root
worker_processes 4
timeout 30
```
Now you need to go to ec2 server. Generate a ssh key and upload it to your github account. This is needed so that the code can be downloaded to the ec2 server, by the capistrano script.
 
```shell
$ ssh-keygen # Keep hitting enter until you create one 
```

This should create rsa_id and rsa_id.pub at ~/.ssh .
```shell
$ cat ~/.ssh/rsa_id.pub
```
Copy the public key created above, and go to Github, select the repo and go to Setting. Paste the pubic key. Then you should be able to access github successfully.

```shell
$ ssh -T git@github.com
```


Then deploy using the below command. 

```shell
$ bundle exec cap development deploy
```






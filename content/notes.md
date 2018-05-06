---
title: Notes
comments: true
weight: -1
type: post
---

#### I will use this space for my notes, basically anything I learn on a daily basis and which I have not yet categorized and put into a post. It can be anything, from simple tips and tricks, to interview questions and puzzles, anything random as well.

###  Webserver and Appllication server?


An app server runs your rails app (your code and keeps your app in memory, handles requests and returns response to web server) [Unicorn, Phusion Passenger, Puma,] ,
A web server handles requests coming from user and redirects, also handles requests for static assets (css, js html files), which need not be redirected to the app server. A webserver can also handle multiple app servers.

Phusion passenger can be configured to be both an app server and a web server, So you can redirect requests to you rails app directly instead of redirecting it to another app server. It is helpful when you are building multiple rails apps. 

### What is Rack? 

The app server is able to talk to the rails app through Rack. Rack is the common language that an app server can talk to rails (sinatra, padrino) app. 

Webserver-> appServer->Rack->Rails Router->Controller
Ngingx-->Uncorn->Rack->router->Controller.

### What is the difference between rails and bin/rails?

When a rails app gets created, it creates 2 files, bin/rails & bin/rake. If you are inside a rails app, the you should use bin/rails, bin/rake. These commands use the right version of rails . Also it will be faster from rails 4. 


### Using Rails Console better
```ruby 
irb(main):001:0> bug = Bug.create(:name => "Some bug name")

irb(main):002:0> app.bug_path bug => "/bugs/1"

irb(main):003:0>  app.get "/bugs/1"
Started GET "/bugs/1" for 127.0.0.1 at 2014-07-09 06:16:21 -0700

irb(main):003:0> puts  app. response. body. first(200 )

irb(main):005:0>  helper. content_tag :h1 , "Hey there"

irb(main):001:0> Article. new
=> #<Article id: nil, title: nil, body: nil, author_id: nil,
created_at: nil, updated_at: nil>
irb(main):002:0>  _. valid?
=> false
```


### How do u speed up bundle install?

Ever felt like bundle install takes a lot of time 
Then this tip is for you 👨‍💻
Install your gems without extra documentation, use flags --no-rdoc and --no-ri
```shell
$ gem install <gem_name> --no-rdoc --no-ri
```

You can automate this with the following in your RoR projects.
Create a file named .gemrc at root level of your app and paste the following snippet.,
```shell
gem: --no-ri --no-rdoc
```
It is said that using these flags on every gem command can break things, so the alternate configuration is:
```shell
install: --no-rdoc --no-ri 
update:  --no-rdoc --no-ri
```


In Addition you can use (bundle install --jobs=4), to install gems in parallel. also use (bundle install --global jobs=4), to make it the default.
for further speed up - use `bundle package` which will package and install gems in vendor/cache and then when doing bundle install --local, will install from this cache only without doing a lookup on rubygems.org


### Extract values with Array#dig and Hash#dig
The new #dig instance methods provide concise syntax for accessing deeply nested data. For example:
```ruby


user = {
  user: {
    address: {
      street1: '123 Main street'
    }
  }
}


user.dig(:user, :address, :street1) # => '123 Main street'
results = [[[1, 2, 3]]]
results.dig(0, 0, 0) # => 1
```
Both of these methods will return nil if any access attempt in the deeply nested structure returns nil:
```ruby
user.dig(:user, :adddresss, :street1) # => nil
user.dig(:user, :address, :street2) # => nil
```
### Hash#transform_values
Let’s say you have a hash & want to change all the values for some reason.
Sort of what you would do with Array#map.
The problem with map is that you will get an array-of-arrays (multi-dimensional array) instead of a hash. Also there is no Hash#map!.
So one solution could be this:
```ruby
h = {apple: 200, coconut: 300}
h.each { |k, v| h[k] = v*2 }
```
In this example we multiply each value times two.
Which results in:
```ruby
{apple: 400, coconut: 600}
```
There is a better way to do this:
```ruby
h.transform_values! { |v| v * 2 }
```

### Kernel#itself
If you told me that this method does not look very interesting at first I would agree with you.
But the reason is that you need to see some good examples.
So let’s say we have an array with repeated words & we want to count them…
While there are many ways to do this, I think this is a great case for testing out our new friend: Kernel#itself.
Example:
```ruby
words = %w(cat cat tiger dog cat)
words.group_by(&:itself).transform_values(&:size)
#Result:
{"cat"=>3, "tiger"=>1, "dog"=>1}
```




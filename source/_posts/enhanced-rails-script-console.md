---
title: Enhancing script/console
tags: [rails, ruby]
type: post
published: true
author: Sandeep Kumar
date: 2010-08-19
---

I Used Rails `rails console` a lot for debugging my rails applications. Every time when I started debugging something, I have to keep my console log[*.log] file to see what corresponding queries being generated to carried out the result I desired.

I found a better solution for that. I enhanced my `rails console`, which enabled on-screen query logging for my rails application. I made a small change to `#{RAILS_ROOT}/script/console.rb` file, which it will look to load any additional ruby file resides in `#{RAILS_ROOT}/console_script/` if available.

I have also included the script I wanted to load in the first place, I find it quite handy to be able to see what kind of queries my commands are generating.

```ruby
  #updates /script/console.rb

  LOAD_HOOK_DIRECTORY = "#{RAILS_ROOT}/console_scripts"
  Find.find( LOAD_HOOK_DIRECTORY ) do |filename|
    if filename =~ /\.rb$/
      puts "Adding #{filename} to load-path"
      libs « " -r #{filename}"
    end
  end
```

```ruby
  #add sql_log.rb file to /console_script/

  def log_to(stream=STDOUT, colorize=true)
    ActiveRecord::Base.logger = Logger.new(stream)
    ActiveRecord::Base.clear_active_connections!
    ActiveRecord::Base.colorize_logging = colorize
  end
  log_to
```

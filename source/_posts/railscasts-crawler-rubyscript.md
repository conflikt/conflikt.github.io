---
title: RailsCast's Crawler - RubyScript
tags: [crawler, hpricot, open-url, railscasts, ruby, ryan bates]
comments: true
type: post
published: true
author: Sandeep
date: 2010-09-08
---

First of all thanks to [RyanBates](http://railscasts.com/) for his valuable contribution towards the rails community with those short and easy-to-understand railscast videos. From Past few months I continuously watching those videos. Yesterday while downloading one of those videos, I realize rather than downloading them when its needed, it would be better to write some short script which will do that for me. After looking for some already existing ruby scripts on internet, I ended up writing my own.

```ruby
  require 'rubygems'
  require 'hpricot'
  require 'open-uri'
  class GetRailsCasts
    def initialize
      @host = "http://railscasts.com/episodes/"
    end
    def start
      1.upto(229){ |eps|
        eps_doc = Hpricot(open("#{@host}#{eps}")) rescue nil
        if eps_doc
          # cd to the folder where u want to store the rails-cast videos
          `cd /Users/sandy/railscasts; wget #{(eps_doc/".download/a").first[:href]}`
        end
      }
    end
  end
```

Comment are most welcome

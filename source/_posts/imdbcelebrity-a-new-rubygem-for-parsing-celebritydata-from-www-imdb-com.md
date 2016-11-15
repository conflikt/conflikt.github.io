---
title: ImdbCelebrity - A new rubygem for parsing celebritydata from www.imdb.com
tags: [rugygems, ruby, imdb]
type: post
published: true
author: Sandeep Kumar
date: 2010-10-21
---

Last week I was working on my scrapper for getting Imdb Celebrity data for some specific purpose. Then, I searched for some available plugin or gem for the same. More surprisingly I was not able to find something what I'm looking for. Then, one idea stuck in my mind, why not create one of my own. Then I started working on this small rubygem named "imdb_celebrity". You can see the public repository of the gem on github [imdb celebrity](http://github.com/sandeepkrao/imdb_celebrity). Also gem is hosted on [Gemcutter](http://rubygems.org/gems/imdb_celebrity/) as well.

Imdb_celebrity is a ruby-gem which is used for scrapping celebrity pages from [imdb.com](http://www.imdb.com) . You can install imdb_celebrity as

~~~bash
  gem install imdb_celebrity
~~~

With current initial release we can search a celebrity with name or we can fetch content for a celebrity with given IMDB id or/and name, by specifying which parser we want to use. Right now we support Hpricot and Nokogiri as parser classes.

### Usages

~~~ruby
  require 'imdb_celebrity'
  # searching a celebrity
  imdb_celebs = ImdbCelebrity::Search.new("Brad Pitt")
  imdb_celebs.celebrities
  # this will return array of celebrity objects.
  # Also you can define the ParserClass which u wanna use [ right now you have choice of using either Nokogiri or Hpricot], as
  imdb_celebs = ImdbCelebrity::Search.new("Brad Pitt", "NokogiriParser")
  # by default it will be HpricotParser
  imdb_celebs.celebrities
  # Fetching data for celebrity
  celeb = ImdbCelebrity::Celebrity.new("0000093", "brad pitt")
  # give 7 digit imdbid number
  celeb.parser
  # => it will give you the type of parser class it using
  celeb.public_methods(false)
  # => will give you the public methods of ImdbCelebrity::Celebrity class only
  celeb.to_s
  # => will return an array of celebrity data items containing name, real_name, biography, nationality, height, url.
~~~

### Requirements

* Hpricot gem should be installed.
* Nokogiri gem should be installed.

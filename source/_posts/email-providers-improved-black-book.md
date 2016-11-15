---
comments: true
title: Black Book Web mail import gem-add mail provider
tags:
  - black-book
  - ruby
  - rubygems
type: post
published: true
author: Sandeep Kumar
date: 2010-08-18
---

Black book is one the easiest way to add web email importing feature in your application. Recently I am working on a Social network cum movie portal site. By default, black book provides support for gmail, yahoo, hotmail, AOL and csv. But as per my requirement I have to add 20-30 mail providers from different countries. I started with [free](http://www.free.fr/)

```ruby
  require "blackbook"
  require "hpricot"
  require 'blackbook/importer/page_scraper'

  class Free < Blackbook::Importer::PageScraper
    # your code
  end
```

### How to login into the users' account for which we are fetching contacts.

```ruby
  def login
    page = agent.get('http://imp.free.fr/')
    form = page.form_with(:name => "implogin")
    form.imapuser = options[:username]
    form.fields[4].value = options[:password]
    page = agent.submit(form,form.buttons.first)
    raise( Blackbook::BadCredentialsError, "That username and password was not accepted. Please check them and try again." ) if page.body =~ /Username and password do not match/
  end

  def scrape_contacts
    unless agent.cookies.find{|c| c.name == "Horde"}
      raise( Blackbook::BadCredentialsError, "Must be authenticated to access contacts." )
    end

    page = agent.get('http://imp.free.fr/horde/turba/browse.php')
    contact_rows = page.search("form[@name=contacts]")
    data = Hpricot(contact_rows.to_s)
    data = (data/"table[1]")/".listitem"
    data.collect do |row|
      { 
        :name  => (row/"a").first.inner_text,
        :email => (row/"a").last.inner_text
      }
    end
  end
```

Points to keep in mind

* scraping contacts differs from one mail provider to another.
* dependencies- mechanize, faster-csv for page scraping
* we can extend  the mail provider within the black book gem as well as with in your application [no need to patch gem]

---
layout: post
title: 'Spree Extend: Updating eligibility criteria for any existing promotion rule'
tags: [spree, rails]
status: publish
type: post
published: true
meta: {}
author: 
comments: true
date: 2012-07-19
---

As I mentioned in my last post [spree-extend](http://whatpeoplethink.wordpress.com/2012/07/12/spree-extend-creating-custom-promotion-rule/), how to create custom promotion rule(s) while working with Spree based e-Commerece website. Last weekend, I faced a situation where I need to update the existing promotion rule to handle some specific condition.

### Scenario

For example, when I select "Product(s)" spree default rule, it gives us the option to choose products manually or choose by product group and its working as expected. Later, my client told me he wants, when he selects by product_group he can actually select some Taxons/Tags so that only the products belongs to that product_group and has taxon(s) listed there are eligible for this promotion.

### Solution

For updating any existing promotion rules in spree there are few generic steps we need to follow,

- Extend the corresponding spree->promotion->rules model class. Here, I created a file `app/models/promotion/rules/product_decorator.rb`

```ruby
  Promotion::Rules::Product.class_eval do
  end
```

- Create the attribute accessors/ accessibles for new attributes you needed.

```ruby
  Promotion::Rules::Product.class_eval do
    has_and_belongs_to_many :taxons, class_name: "::taxon", join_table: "taxons_promotion_rules", foreign_key: "promotion_rule_id"
    attr_accessible :taxon_ids_string
    //setter and getter methods
    def taxon_ids_string
      self.taxon_ids.join(",")
    end
    def taxon_ids_string=(string)
      self.taxon_ids = string.split(",").map(&:strip)
    end
  end
```

* update the corresponding View file for the same rule in admin/promotions/rules/_product.html.haml or .erb if you are using erb. Add the required code for handling the same. In My case I added one TokenInput object to get multiple taxons selection. Add code something like this... where-ever you think is more suitable for you.

```haml
  %p{:class=>"field products_rule_taxons"}
    %label
      = "Choose Taxons"
      = taxon_picker_field "#{param_prefix}[tag_ids_string]", promotion_rule.taxon_ids_string
```

There are 2 different ways of adding this to view file. One, you can write a decorator and tell before/after which object you want to put this code. Second, you can re-write the complete file and do whatever you need. Upto you.

Also, `taxon_picker_field` is a helper method which I created for creating the `tokenInput/tokenizer` object. Create the `AdminBaseHelper` decorator file, if not exists and put the following code or something like that,

```ruby
  Admin::BaseHelper.module_eval do
    def taxon_picker_field(name, value)
      taxons = value == "" ?  [] : Taxon.where("id in ('#{value}')")
      taxon_names_hash = taxons.inject({}){|memo,item| memo[item.id] = item.name; memo}
      %(tag_picker_initializer();).html_safe
    end
  end
```

* Update the eligible? and/or eligible_products methods as per your rule updations. Here in my case I just need to change the eligible_products method. Add the following code in `app/models/promotion/rules/product_decorator.rb`

~~~ruby
  def eligible_products
    if product_group
      return self.taxons.collect(&amp;:products).flatten
    end
    products
  end
~~~

Or something like that as per your requirements

And yes, We are good to go and the new rule criteria will be applied from now on-wards. Good luck, In case of any query, feel free to ask here or email me at [Sandeep Kumar](mailto:skr.ymca@gmail.com)

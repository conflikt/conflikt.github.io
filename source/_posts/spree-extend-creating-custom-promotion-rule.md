---
layout: post
title: 'Spree Extend: Creating Custom Promotion Rule'
tags: [spree, rails, spree extend]
status: publish
type: post
published: true
author: Sandeep Kumar 
comments: true
date: 2012-07-12
---

I'm working on an E-commerce website for sometime now. We are usingi [spree](http://spreecommerce.com/) as backbone of the project and customizing where and when its required. Spree is quite good and fully functioning E-commerce Rails engine which give you base to build a e-commerce website quickly and with lesser efforts.

One of the main features of any e-commerce website is Promotions/Coupons. Spree also has a built in spree promotion module which is ready to use with predefined rules and adjustment calculators. Being an ADMIN I can define any new promotion rules i.e. first order, user specific, products specific etc. Also I can define the actions like ALL or ANY. You can have a look on spree_promo module at [spree_promo](https://github.com/spree/spree/tree/master/promo) and see how it works.

Spree already provides some 4-5 predefined Promotions Rules on the basis of which every order get cross-checked whether some adjustments has to be done or not. Most of the time these rules/calculators are more than enough in general usage, but sometimes we need few very specific CUSTOM rules to be defined. Here I'm going to explain how easy it is to create new spree promotion rules.

#### Create Promotion Rule
1. create a model file app/models/spree/promotion/rules/my_custom_promotion.rb

~~~ruby
  module Spree
    class Promotion::Rules::MyCustomPromotion &lt; PromotionRule
      // required associations or preferences if any
      // required attribute accessible if you need to protect some attributes from mass assignment
      // match polices and operators
      MATCH_POLICIES = %w(any all) // as per your requirement
      def eligible?(order, options={})
        // This method is the place where every order is being verified whether its eligible for any promotional discount
      end
    end
  end
~~~

2. Add this custom promotion rule to default spree promotion rules list. Write the following code in either `application.rb` or some initializer

~~~ruby
  initializer "spree.promo.register.promotions.rules" do |app|
    app.config.spree.promotions.rules += [Spree::Promotion::Rules::MyCustomPromotion]
  end
~~~

3. Add the translations for name & description for custom rule as spree use the standard view for all with Translation method `t('name')` or `t('description')`. Write the following code in your `config/locales/en.yml` (or any other locale file if you are having multilingual products).

~~~yaml
  en:
    promotion_rule_types:
      product_by_tag:
        name: Products By MyCustomPromotion
        description: Add products from MyCustomPromotion
~~~

4. Create the view partial file for the custom promotion rule. Add a file `app/admin/promotions/rules/_my_custom_promotion.html.erb` or haml or something else depending on the template engine you are using for views. And, restart your application server and you are good to go. Now whenever you are going to edit the Promotion, in the promotions rules list you will see you custom rule also available.

* if you want some custom price adjustments/calculations as well, then you have to create a new calculator. E.g. If I want to define some flat percentage benefit on my custom promotion we have to create `app/models/spree/calculator/flat_percentage.rb`

~~~ruby
  module Spree
    class Calculator::FlatPercetange &lt; Calculator
      // preferences if there are any.
      // attributes accessibles
      def compute(object)
        // main method where we have to compute the adjustments we have to made.
      end
    end
  end
~~~

I hope it helped someone. In case of any query, please feel free to ask here or drop me a mail at [Sandeep Kumar](mailto:skr.ymca@gmail.com?subject=Re:Creating custom spree promotion rule)

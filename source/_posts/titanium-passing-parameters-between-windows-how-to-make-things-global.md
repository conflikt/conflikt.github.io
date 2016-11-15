---
layout: post
title: "Titanium : Passing parameters between windows [How to make things global]"
tags: [titanium]
status: publish
type: post
published: true
comments: true
author: Sandeep Kumar
date: 2011-12-09
---

While working on any titanium application, one of the common possibilities is to use some variables/constants across the windows. In Titanium, windows use to have their on context. That means the variables/values available on one window are not available on the others linked windows by default. So, if we are required to pass parameters between windows,  there are many different ways to achieve this. So, choosing a perfect approach for passing variables/parameters within windows depends on the requirement we are having. In our last project we had used few different approaches as per our requirements. Few of them are :

#### Ti.App.Properties:

There are few cases where we need to store values to be used everywhere in the application, and we want to retain those values even when application is not-running/ in-background. The App Properties module is used for storing application related property/value pairs which persist beyond application sessions.

We can set the property/value pair as:

~~~ javascript
  Titanium.App.Properties.setString("my_prop","cool");
~~~

Now, as property/value pair is set. Now I can simply retrieve that value where I want in the application as:

~~~ javascript
  var value = Titanium.App.Properties.getString("my_prop");
~~~

While setting properties in Ti.App.Properties, we have to make sure few things:

* we are actually making things globally available, means whatever values we are storing, should not conflict with already existing global titanium constants, otherwise functionality will suffer. For more information, check Titanium documentation at [Titanium.App.Properties](https://developer.appcelerator.com/apidoc/mobile/1.0/Titanium.App.Properties)

#### Ti.App:

There are cases, when we need few values to be available all over the application, but when we close the application, those values should not be available any-more. For example,  while working on an application I need to access the current locale for the device for different purposes. But when I closes the application, I don't need that value to persist as use can change the locale in-between as well. So better to make request to get these kind of values when app initializes and persist those values throughout the application session.

We can declare methods and variables to be globally available with Ti.App as:

~~~ javascript
  Ti.App.sample_variable = "xxxxxx";
  Ti.App.sample_function = function(_args){ //your code for the function };
~~~

Then we can use this variable/method anywhere in our application. It will be available in the application session.

#### App Level Global Declaration

For past weeks I was wondering about what are the best ways of writing titanium javascript code to make it more generic, reusable and more modular. Tweetanium, the sample application build by titanium/appcelerator team to help people, posses a new approach for design/creation of titanium based applications. Approach is to maintain an Application Level NAMESPACE and write each and every single peace of code wither to that namespace or sub-namespaces to that. e.g.

In app.js of your application,

~~~ javascript
  //app.js
  //suppose name of my application is sample, so defining the global namespace or placeholder for my application
  var sample = {};
~~~

Now I can define my UI, models, controllers methods and variables in the corresponding namespace or placeholder to make them more structured. As,

~~~ javascript
  sample.UI = {};
~~~

Now if I want to declare any method or variables, I can place them in corresponding files and assign all the methods/ variables to their parent placeholder.

~~~ javascript
  //UI.js</span></p>
  (function(){
    sample.UI.xxxx = function(_args){
      //write you code you want to use.
    };
  })();
~~~

For knowing more about how to write MVC structure applications with titanium, stay tuned, as I will be back soon with one more blog post for the same

#### Passing parameters within successive windows

If we just need to pass parameters/values between 2 successive windows, then it can be done via sending values as a part of window object it self. Like. for example I'm working on a map related stuff and when I click on a button which leads me to the next window which shows some data corresponds to my geolocation position. Then while creating the window element we can pass those values as part of window object itself. As:

~~~ javascript
  // window1.js
  .........
  var button = Ti.UI.createButton({
    label: "button1",
    height: "20dp",
    width: "100dp"
  });
  
  button.addEventListener("touchstart", function(){
    var window2 = Ti.UI.createWindow({url:"window2.js"});
    window2.sample_value = "xxxxxx";
    Ti.UI.currentTab.open(window2, {animated:true});
  });
~~~

~~~ javascript
  //windows2.js
  var window = Ti.UI.currentWindow;
  Ti.API.info(window.sample_value);
  //this will return "xxxxxx" as output;
~~~
These are the few ways of passing variables globally which I used till now. For any query Contact me anytime, or reply back.

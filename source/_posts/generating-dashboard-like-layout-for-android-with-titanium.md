---
layout: post
title: Generating Dashboard like layout for android with Titanium
categories:
- Android
- titanium
tags: []
status: draft
type: post
published: true
meta: {}
author: Sandeep Kumar
comments: true
date: 2011-12-12
---
<p>From sometime, I was working on a Titanium project, where we apply so many tweaks and did so much of research work. One the requirement of the application to make an dashboard kind of layout for landing screen of the application. For iphone-titanium, The Dashboard View provides a view that supports the ability to have Springboard-like view of icons which can be reordered by dragging and can contain multiple pages of icons in a scrollable view. The Dashboard View is created by the method <a href="http://developer.appcelerator.com/apidoc/mobile/latest/Titanium.UI.createDashboardView-method.html">Titanium.UI.createDashboardView</a>.</p>

```javascript
  for (var x=0;x<1;x++)
  {
    for (var c=0;c<labels.length;c++)
    {
        var item = Titanium.UI.createDashboardItem({
            image:'../images/dashboard/'+labels[c]+'.png',
            selectedImage:'../images/dashboard/'+labels[c]+'_on.png',
            label:labels[c]
        });
        if (c==0) item.badge = 0;
        data.push(item);
    }
  }
```

<p>But there is no alternate for the same for android-titanium. So we decided to create our own customized dashboard implementation.  Also, we were using tweatanium kind of design flow for the application. Later I decide to move the dashboard code as a module out of the application, so that we can plug it anywhere when required and re-use that.
To utilize the DashBoard module, first create the application level hash variable, so that we can include this code/module/file in app.js and use it everywhere else.
In your app.js initialize the global variable
</p>

```javascript
  application.dashboard = {};
  Ti.include("path_to_dir/dashboard.js");
  // Your rest of the code of app.js
```
<p>Before starting the code for designing the dashboard, we have to make our mind, whether we want 3xN or 4xN dashboard layout. Because correspondingly we need to resize/redesign our images for dashboard icons. Whiling using the dashboard module, we need to take care about few constants/configurable variables which are helpful for writing the code. e.g.,</p>
* dashboard_items  [{..}, {...},...] is a array of hashes which use keep track of the different icons which we are going to display on the dashboard, which contains 3 main values related to each icon <TITLE, ICON-LOGO, JS-file>.

```javascript
dashboard_items = [{title: 'Home', icon-logo: 'images/home.png', js-file: 'xxxxx/home.js'}......];
```

* incrementer is vertical spacing between icons on the dashboard
* we can set font, color, font-family for dashboard icons as well. etc

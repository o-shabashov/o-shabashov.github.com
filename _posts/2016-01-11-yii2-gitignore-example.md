---
layout: post
title: "Yii2 .gitignore example"
description: "Just another one working example of .gitignore for Yii2"
category: 
tags: [Yii2, git, .gitignore]
---
{% include JB/setup %}

>Just another one working example of .gitignore for Yii2

{% highlight sh %}
# phpstorm project files
.idea/**

# OS cache
Thumbs.db
.DS_Store

# composer vendor dir
vendor/**

# Uploads dir
web/media

# Yii temp directories
web/assets/**
runtime/**
web/minify/**

# composer itself is not needed
composer.lock

# Gulp
package.json
npm-debug.log
node_modules/**

# Components from node.js and composer
components/jquery
components/select2
components/require.config.js
components/require.css
components/require.js
components/require-built.js

# Codeception
tests/codeception/_support/_generated/*
{% endhighlight %}
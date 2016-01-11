---
layout: post
title: "Yii2 hasAttribute VS hasProperty"
description: "Yii2 compare ActiveRecord->hasAttribute VS Model->hasProperty"
category: 
tags: [Yii2, hasAttribute, hasProperty]
---
{% include JB/setup %}

> Yii2 compare ActiveRecord->hasAttribute VS Model->hasProperty

{% highlight php %}
<?php
// class ContactForm extends Model
$contact = new ContactForm();

// class User extends \yii\db\ActiveRecord
$user = new User();

isset($contact->email);         // false
$contact->hasProperty('email'); // true

isset($user->email);            // false
$user->hasProperty('email');    // false
$user->hasAttribute('email');   // true

$contact->email = 'test';
$user->email    = 'test';

isset($contact->email);         // true
$contact->hasProperty('email'); // true

isset($user->email);            // true
$user->hasProperty('email');    // false
$user->hasAttribute('email');   // true

{% endhighlight %}
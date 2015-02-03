---
layout: post
title: "Yii2 ActiveScopeTrait"
description: "How-to return back easy scope based query thought Model"
category: Yii2
tags: [Yii2, ActiveQuery, Yii2VM, scope]
---
{% include JB/setup %}

>First of all, install [Yii2VM](//{{site.host}}/pages/yii2-voodoo-mobile.html)

The preferred way to install this extensions is through [composer](http://getcomposer.org/download/).

Either run

{% highlight sh %}
php composer.phar require "voodoo-mobile/yii2vm" "*"
{% endhighlight %}

or add

{% highlight json %}
"voodoo-mobile/yii2vm" : "*"
{% endhighlight %}

to the require section of your application's `composer.json` file.

>Add in model

{% highlight php %}
<?php
use yii2vm\db\ActiveQuery;
use yii2vm\db\ActiveScopeTrait;

class Post extends \yii\db\ActiveRecord
{
    use ActiveScopeTrait;

    /**
     * @param ActiveQuery $query
     *
     * @return static
     */
    public static function scopeActive(ActiveQuery $query)
    {
        return $query->andWhere(['status' => Post::STATUS_ACTIVE]);
    }
}
{% endhighlight %}

> Use it in controller

{% highlight php %}
<?php
public function actionIndex()
{
    $dataProvider = new ActiveDataProvider([
        'query'      => Post::find()->active()
    ]);

    return $this->render('index', ['dataProvider' => $dataProvider]);
}
{% endhighlight %}

That's all.
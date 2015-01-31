---
layout: post
title: "Yii2 search in ActiveDataProvider"
description: "How-to search by where in ActiveDataProvider, Yii2"
category: Yii2
tags: [yii2, activeDataProvider, search]
---
{% include JB/setup %}


>Controller

{% highlight php %}
<?php
public function actionIndex()
{
    $searchModel  = new Post();
    $dataProvider = $searchModel->search(Yii::$app->request->queryParams);

    return $this->render('index', [
        'searchModel'  => $searchModel,
        'dataProvider' => $dataProvider,
    ]);
}
{% endhighlight %}


>Model method

{% highlight php %}
<?php
public function search($params)
{
    $query = Post::find();

    $dataProvider = new ActiveDataProvider([
        'query' => $query,
    ]);

    if (!($this->load($params) && $this->validate())) {
        return $dataProvider;
    }

    $query->andFilterWhere([
        'id'         => $this->id,
        'user_id'    => $this->user_id,
        'created_at' => $this->created_at,
        'updated_at' => $this->updated_at,
    ]);

    $query->andFilterWhere(['like', 'title', $this->title])
        ->andFilterWhere(['like', 'description', $this->description]);

    return $dataProvider;
}
{% endhighlight %}


>View

{% highlight php %}
<?= GridView::widget([
    'dataProvider' => $dataProvider,
    'filterModel' => $searchModel,
    'columns' => [
        ['class' => 'yii\grid\SerialColumn'],
        'id',
        'user_id',
        'title',
        'description',
        'created_at',
        'updated_at',
        ['class' => 'yii\grid\ActionColumn'],
    ],
]); ?>
{% endhighlight %}

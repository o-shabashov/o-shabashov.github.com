---
layout: post
title: Simple capistrano deploy
description: Настроить деплой проекта на удаленный сервер 
category: Deploy
tags: [capistrano, apache, gem, git, git-submodules]
---
{% include JB/setup %}

### Задача - настроить деплой проекта на удаленный сервер

>Шаг 1. Установка capistrano на свою локальную машину

{% highlight sh %}
sudo gem install capistrano --no-ri --no-rdoc
{% endhighlight %}
  
>Шаг 2. (опционально) Установка поддержки git submodules для capistrano

{% highlight sh %}
sudo gem install capistrano-git-submodule-strategy --no-ri --no-rdoc
{% endhighlight %}

>Шаг 3. Подключение capistrano к своему проекту

В любом месте своего проекта выполнить команду:
{% highlight sh %}
cap install
{% endhighlight %}

Эта команда создаст необходимые для capistrano файлы настроек. Если capistrano не будет разворачивать на staging, то файл `config/deploy/staging.rb` можно спокойно удалить.

>Шаг 4. Вносим необходимые изменения в файл `config/deploy.rb`

{% highlight ruby %}
# config valid only for Capistrano 3.1
lock '3.2.1'
set :application, 'echo'
set :repo_url, 'git@bitbucket.org:voodoo-mobile/echo.git'

# Default deploy_to directory is /var/www/my_app
# set :deploy_to, '/var/www/capistrano/echo'

# Vcs settings
set :scm, :git

# If git submodules exists
set :git_strategy, Capistrano::Git::SubmoduleStrategy

set :deploy_via, :remote_cache
set :format, :pretty

# Default value for :log_level is :debug
set :log_level, :info

# Default value for :pty is false
set :pty, true

# Default value for :linked_files is []
# set :linked_files, %w{config/database.yml}

# Default value for linked_dirs is []
# set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}

# Default value for default_env is {}
# set :default_env, { path: "/opt/ruby/bin:$PATH" }

# Default value for keep_releases is 5
# set :keep_releases, 5

namespace :deploy do
  desc 'Restart application'
  task :restart do
    on roles(:app), in: :sequence, wait: 5 do
      # Your restart mechanism here, for example:
      # execute :touch, release_path.join('tmp/restart.txt')
    end
  end
  after :publishing, :restart
  after :restart, :clear_cache do
    on roles(:web), in: :groups, limit: 3, wait: 10 do
      # Here we can do anything such as:
      # within release_path do
      #   execute :rake, 'cache:clear'
      # end
    end
  end
end
{% endhighlight %}

>Шаг 5. Вносим необходимые изменения в файл `config/deploy/production.rb`

{% highlight ruby %}
# Simple Role Syntax
# ==================
# Supports bulk-adding hosts to roles, the primary server in each group
# is considered to be the first unless any hosts have the primary
# property set.  Don't declare `role :all`, it's a meta role.
# role :web, %w{deploy@example.com}
# role :db,  %w{deploy@example.com}
role :app, %w{deploy@example.com}

# Extended Server Syntax
# ======================
# This can be used to drop a more detailed server definition into the
# server list. The second argument is a, or duck-types, Hash and is
# used to set extended properties on the server.
server 'example.com', user: 'deploy', roles: %w{web app}
set :branch, "release"

# Custom SSH Options
# ==================
# You may pass any option but keep in mind that net/ssh understands a
# limited set of options, consult[net/ssh documentation](http://net-ssh.github.io/net-ssh/classes/Net/SSH.html#method-c-start).

# Global options
# --------------
set :ssh_options, {
 keys: %w(/home/user_name/.ssh/id_rsa),
 forward_agent: false,
 # auth_methods: %w(password)
}

# And/or per server (overrides global)
# ------------------------------------
# server 'example.com',
#   user: 'user_name',
#   roles: %w{web app},
#   ssh_options: {
#     user: 'user_name', # overrides user setting above
#     keys: %w(/home/user_name/.ssh/id_rsa),
#     forward_agent: false,
#     auth_methods: %w(publickey password)
#     # password: 'please use keys'
#   }
{% endhighlight %}

`ВАЖНО!` Измените имя юзера, адрес сервера и путь до SSH ключа.

> Шаг 6. Подготовка боевого сервера, на который будем делать деплой

Допустим, что на боевой мы вошли под логином `ubuntu`.

>Выставляем нормальные права на директорию `/var/www`:

{% highlight sh %}
sudo chown -R ubuntu:www_data /var/www
sudo chmod -R g+rwX /var/www
sudo chmod g+s /var/www
{% endhighlight %}

>Создаем пару SSH ключей, чтобы ходить до репозитория:

{% highlight sh %}
ssh-keygen -t rsa 
cat ~/.ssh/id_rsa.pub
{% endhighlight %}

Вывод предыдущей команды скопировать и вставить как Deployment ключ в наш репозиторий.

>Настраиваем Веб-сервер:

{% highlight apache %}
<VirtualHost *:80>
  ServerName example.com

  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/project-name/current/project-web-root

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
  RewriteEngine on

  <Directory var/www/project-name/current/project-web-root>
    Options FollowSymLinks Indexes MultiViews
    Require all granted
    AllowOverride All
  </Directory>

</VirtualHost>
{% endhighlight %}

>`ВАЖНО!` Директория `current` - это симлинк, который создает capistrano и ссылается этот симлинк на последний деплой.

>Шаг 5. Запуск деплоя на локальной машине

Переходим в директорию, в которой делали `cap install` и выполняем:
{% highlight sh %}
cap production deploy
{% endhighlight %}

>Всё.
---
layout: post
title:  "Первый пост"
date:   2017-06-11 16:30:00 +0300
categories: guide
tags: ruby rbenv jekyll
---

{% comment %}
{% for tag in page.tags %}
* {{ tag }}
{% endfor %}
{% endcomment %}


Первый пост будет о том как создать такой блог и как его развернуть на Github'e.
Данный блог создан с помощью статического генератора Jekyll. Для его создания в 
системе (конечно же в Linux) должен быть установлен интерпретатор Ruby. Ruby можно 
установить [разными способами](https://www.ruby-lang.org/ru/documentation/installation/#managers), 
здесь будет показана установка с помощью *rbenv + ruby-build*.

## Установка [rbenv](https://github.com/rbenv/rbenv) и [ruby-build](https://github.com/rbenv/ruby-build)

Для установки `rbenv` в терминале выполняем следующие команды:
```bash
# Установить все необходимые библиотеки для сборки Ruby из исходного кода
$ sudo apt-get install git-core curl zlib1g-dev build-essential \
libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 \
libxml2-dev libxslt1-dev libcurl4-openssl-dev \
python-software-properties libffi-dev

# Клонируем репозиторий в скрытую папку .rbenv, которая будет находится в домашнем каталоге
$ git clone https://github.com/rbenv/rbenv.git ~/.rbenv

# Пытаемся откомпилировать динамическое расширения bash'a для увеличения скорости 
# (может не получится, если будут ошибки, то переходим к следующему шагу)
$ cd ~/.rbenv && src/configure && make -C src

# Обновляем переменную окружения $PATH и записываем ее в .bash_profile (если Ubuntu Desktop, то в .bashrc)
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
$ source ~/.bash_profile
```

После этого должна выполняется команда `rbenv`, ответ должен быть примерно таким:
```bash
artem@ubuntu:~$ rbenv 
rbenv 1.1.0-7-g755c820
Usage: rbenv <command> [<args>]

Some useful rbenv commands are:
   commands    List all available rbenv commands
   local       Set or show the local application-specific Ruby version
   global      Set or show the global Ruby version
   shell       Set or show the shell-specific Ruby version
   rehash      Rehash rbenv shims (run this after installing executables)
   version     Show the current Ruby version and its origin
   versions    List all Ruby versions available to rbenv
   which       Display the full path to an executable
   whence      List all Ruby versions that contain the given executable

See `rbenv help <command>` for information on a specific command.
For full documentation, see: https://github.com/rbenv/rbenv#readme
```

Далее устанавливаем `ruby-build` как плагин к `rbenv`:
```bash
# Просто клонируем репозиторий в папку ~/.rbenv/plugins/ruby-build
$ git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
```

Разлогиваемся (`$ logout`) (или выходим, закрываем терминал) и заново заходим.

Следующим шагом устанавливаем Ruby:

```bash
# Получаем список всех версий ruby, которые можно установить (здесь список сокращен)
$ rbenv install -l
Available versions:
  1.8.5-p52
  1.8.5-p113
  1.8.5-p114
  1.8.5-p115
  1.8.5-p231
  1.8.6
  1.8.6-p36
  ...
  2.3.0
  2.3.1
  2.3.2
  2.3.3
  2.3.4
  2.4.0-dev
  2.4.0-rc1
  2.4.0
  2.4.1
  2.5.0-dev
  ...
  rbx-3.74
  rbx-3.75
  ree-1.8.7-2011.03
  ree-1.8.7-2011.12
  ree-1.8.7-2012.01
  ree-1.8.7-2012.02
  topaz-dev
  
# Установка Ruby версии 2.4.1 (занимает где-то ~7 минут)
$ rbenv install 2.4.1
Downloading ruby-2.4.1.tar.bz2...
-> https://cache.ruby-lang.org/pub/ruby/2.4/ruby-2.4.1.tar.bz2
Installing ruby-2.4.1...

# Проверяем список установленных версий
$ rbenv versions
  2.4.1
```

Сейчас в качестве глобально интерепретатора установлен системный, но нам нужен Ruby,
который мы только-что установили.
```bash
$ rbenv global
system
$ rbenv global 2.4.1
$ rbenv global 
2.4.1
$ ruby -v
ruby 2.4.1p111 (2017-03-22 revision 58053) [x86_64-linux]
```

## Установка [jekyll](https://jekyllrb.com/) и настройка сайта

Для установки `jekyll` и его настройке выполняем следующие команды в терминале:
```bash
$ gem install jekyll bundler
$ jekyll new myblog
$ cd myblog
artem@ubuntu:~/myblog$ ls # Содержимое папки
about.md  _config.yml  Gemfile	Gemfile.lock  index.md	_posts
```

Далее редактируем файл *_config.yml* в своем любимом редакторе. Я буду использовать 
Vim (как его настроить постараюсь написать в следующих статьях). `vim _config.yml`

![_config.yml in vim]({{ page.img }}/config_jekyll_in_vim.png)

Запускаем сервер для проверки `$ jekyll serve --host 0.0.0.0`. И смотрим свой блог 
по адресу: `http://localhost:4000` 

Создаем папку [`assets`](https://jekyllrb.com/docs/posts/#including-images-and-resources),
а в ней `images`, в которую будем складывать изображения для постов: `$ mkdir -p ./assets/images`
и добавим ее в `_config` в секцию [defaults](https://jekyllrb.com/docs/configuration/#front-matter-defaults).


И последнее, что нужно сделать при настройке, это убрать стандартную тему 
и поставить тему [Lanyon](http://lanyon.getpoole.com/about/).
Для этого выполняем такие команды:
```bash
$ cd ~
$ wget https://github.com/poole/lanyon/archive/v1.0.0.zip
$ unzip v1.0.0.zip -d lanyon # (если нет unzip, то устанавливаем его: sudo apt-get install unzip)
# Копируем файлы
$ cp ~/lanyon/lanyon-1.0.0/.gitignore ~/myblog/.gitignore
# Копируем html-файлы ($ cp ~/lanyon/lanyon-1.0.0/index.html  ~/myblog/index.html)
$ cp ~/lanyon/lanyon-1.0.0/*.html ~/myblog/
$ cp ~/lanyon/lanyon-1.0.0/atom.xml  ~/myblog/atom.xml
$ cp -r ~/lanyon/lanyon-1.0.0/public ~/myblog/public
$ cp -r ~/lanyon/lanyon-1.0.0/_includes ~/myblog/_includes
$ cp -r ~/lanyon/lanyon-1.0.0/_layouts ~/myblog/_layouts
# В итоге должно получится как-то так
artem@ubuntu:~$ cd myblog/
artem@ubuntu:~/myblog$ ls
404.html  assets    _config.yml  Gemfile.lock  index.html  _layouts  public
about.md  atom.xml  Gemfile	 _includes     index.md    _posts    _site
```

Запуск в production: `$ JEKYLL_ENV=production bundle exec jekyll serve --host 0.0.0.0`


```bash
$ sudo jekyll serve --host 0.0.0.0 --port 8080 --detach &
$ sudo pkill -f jekyll
# or 
$ ps ax | grep 'jekyll' # and kill -p <PID>
# Edit post
$ sudo vi ./_posts/2017-04-27-my-first-post.md
# and build if server running
$ sudo jekyll build
```


## Развертывание на Github

[Создаем репозиторий](https://github.com/new) с именем `<username>.github.io`, 
где `<username>` - логин профиля Github'a. Далее выполняем следующие команды:

```bash
$ cd ~/myblog
$ git init
$ git add .
$ git commit -m "Init"
$ git remote add origin https://github.com/Yegorov/yegorov.github.io.git
$ git push origin master
```

Теперь сайт доступен по адресу: [https://yegorov.github.io](https://yegorov.github.io)

## Ссылки

### rbenv

* [Инструкция по установки rbenv на русском](https://medium.com/@tuwilof/%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0-ruby-%D1%81-%D0%BF%D0%BE%D0%BC%D0%BE%D1%89%D1%8C%D1%8E-rbenv-%D0%B2-ubuntu-fa7d4b82a67f)
* [Инструкция по установки rbenv на английском](https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-with-rbenv-on-ubuntu-14-04)

### jekyll

* [Конфигурация](https://jekyllrb.com/docs/configuration/)
* [Front Matter](https://jekyllrb.com/docs/frontmatter/)
* [Написание постов](https://jekyllrb.com/docs/posts/)
* [Тема Lanyon](https://github.com/poole/lanyon), [другие темы на github'e](https://github.com/search?o=desc&p=2&q=jekyll+theme&s=stars&type=Repositories&utf8=%E2%9C%93)
* [Jekyll cheatsheet](http://ricostacruz.com/cheatsheets/jekyll.html)
* [Liquid - Safe, customer-facing template language for flexible web apps](https://shopify.github.io/liquid/)
* [Написание плагина](https://tuananh.org/2014/08/04/writing-your-first-jekyll-plugin/)

### Развертывание на github

* [GitHub Pages](https://pages.github.com/)
* [Using Jekyll as a static site generator with GitHub Pages](https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/)


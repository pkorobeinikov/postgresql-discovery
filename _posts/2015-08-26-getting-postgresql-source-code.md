---
layout: post
title:  "Получение исходного кода"
date:   "2015-08-26 22:43:12"
---
Перед тем, как начать погружение давайте договоримся о структуре каталогов в нашем рабочем пространстве.
Я предпочитаю организовывать своё рабочее пространство,
так чтобы каждый проект находился в собственном каталоге с доменным именем сайта проекта:

{% highlight bash %}
...
├── memcached.org
│   ├── build
│   └── memcached
├── nginx.org
│   ├── build
│   ├── nginx
│   └── nginx-tests
├── postgresql.org
│   ├── build       # --prefix сборки
│   ├── data        # тестовые базы данных
│   ├── postgresql  # рабочая копия
...
{% endhighlight %}

Прежде всего нам понадобится рабочая копия репозитория с исходными кодами Postgresql.
Репозиторий весит 420 мегабайт, поэтому наберитесь терпения &mdash; git потребует какое-то время для клонирования.

Можно ускорить получение рабочей копии, указав параметр ```--depth 1``` команды ```git clone```. Затем, когда появится время и возможность, выполнить ```git fetch --unshallow``` и получить все оставшиеся коммиты.

{% highlight bash %}
$ git clone git://git.postgresql.org/git/postgresql.git
# или зеркало на github.com:
$ git clone git@github.com:postgres/postgres.git
{% endhighlight %}

Что нас ждёт внтури? Я прокомментировал содержимое трех, пожалуй, самых важных каталогов.

{% highlight bash %}
$ tree -d -L 1 src/ doc/ contrib/
src/
├── backend     # Исходный код самого сервера postgresql
├── bin         # Исходный код утилит: initdb, pg_ctl, psql и пр.
├── common      # Общие функции, их немного и они довольно обобщённые
├── include     # *.h-файлы
├── interfaces  # Исходный код ECPG и libpq
├── makefiles   # Системно-зависимые makefile'ы
├── pl          # Хэндлеры pl-языков
├── port        # Много кода для поддержки Windows
├── template    # Системно-зависимые параметры
├── test        # Тесты
├── timezone    # Адаптированная версия timezone
├── tools       # Различные инструменты разработчика
└── tutorial    # Примеры расширений на C и туториал по SQL
doc/
└── src         # Исходники документации в формате docbook
contrib/        # Пользовательские расширения
├── adminpack
├── auth_delay
...
├── vacuumlo
└── xml2

63 directories
{% endhighlight %}

В каталоге ```contrib/``` на данный момент надодится 49 расширений:

{% highlight bash %}
$ ls -d contrib/*/ | wc -l
{% endhighlight %}

Загляните в документацию[^doc],
чтобы подробнее узнать о назначении этих модулей.

----

[^doc]: [Документация по contrib-модулям](http://www.postgresql.org/docs/devel/static/contrib.html)

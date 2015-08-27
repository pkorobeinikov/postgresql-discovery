---
layout: post
title:  "Конфигурация, сборка и установка"
date:   "2015-08-27 19:08:13"
---
Для сборки вам понадобятся несколько пакетов (имена пакетов приведены из Debian-based дистрибутива):

* libreadlineX-dev, где X &mdash; 5 или 6
* zlib1g-dev
* bison
* flex

Выполните конфигурацию в каталоге с рабочей копией репозитория.

{% highlight bash %}
> ./configure --prefix=$YOUR_WORKSPACE_PATH/postgresql.org/build  \
    --enable-debug      \
    --enable-depend     \
    --enable-cassert
{% endhighlight %}

Для ускорения сборки можно выполнить её в несколько потоков:

{% highlight bash %}
> make -j 8
{% endhighlight %}

Так как для команды ```./configure``` была задана опция ```--prefix```,
можно безбоязненно выполнять команду ```make install```.
Установка пройдёт по указанному пути,
и мусор не будет разбросан по системным каталогам:

{% highlight bash %}
> make install
{% endhighlight %}

На этом установка завершена. Давайте посмотрим на каталог ```build/```:

{% highlight bash %}
> tree -L 3 -F build/
build/
├── bin/                            # Утилиты из поставки Postgres
│   ├── ...
│   ├── pg_config*
│   ├── pg_ctl*
│   ├── pg_dump*
│   ├── ...
│   ├── pg_restore*
│   ├── ...
│   ├── postgres*                   # Сервер Postgres
│   ├── postmaster -> postgres*
│   ├── psql*                       # Клиент psql
│   └── vacuumdb*
├── include/                        # Заголовочные файлы для разработчиков
│   ├── ...
│   ├── internal/
│   ├── ...
│   │   ├── libpq/
│   │   ├── ...
│   ├── libpq/
│   │   └── libpq-fs.h
│   ├── libpq-events.h
│   ├── libpq-fe.h
│   ├── pg_config.h
│   ├── ...
│   ├── server/
│   │   ├── ...
│   └── ...
├── lib/                            # Статические и динамические библиотеки
│   └── ...
└── share/                          # Конфигурации словарей, временных зон и пр.
    └── ...

60 directories, 245 files
{% endhighlight %}

На этом процесс установки завершен.
Мы собрали и установили собственную копию сервера Postgresql, готовую к отладке и изучению с помощью gdb (или lldb для счастливых обладателей компьютеров с ).

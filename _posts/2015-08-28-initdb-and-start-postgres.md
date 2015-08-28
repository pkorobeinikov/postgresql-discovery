---
layout: post
title:  "Создание базы данных и запуск сервера"
date:   "2015-08-28 15:41:02"
---
Мы вплотную приблизились к использованию только что собранного и установленного сервера Postgres.

Для удобства можно добавить в переменную окружения ```$PATH``` путь до каталога ```bin/``` из нашей установки.

{% highlight bash %}
$ export $PATH=$PATH:$YOUR_WORKSPACE_PATH/postgresql.org/build/bin
{% endhighlight %}

Каталог с базой данных будет размещён в каталоге ```data/``` рядом с каталогом ```build/``` и рабочей копией репозитория:

{% highlight bash %}
$ cd $YOUR_WORKSPACE_PATH/postgresql.org/ && ls -1
build           # Каталог сборки
data            # Здесь будут находится файлы базы данных
postgresql      # Рабочая копия репозитория postgresql
{% endhighlight %}

Теперь создадим базу данных:

{% highlight bash %}
$ initdb -E utf8 -U `id -nu` data
{% endhighlight %}

Запуск ```initdb``` подсказывает, как можно запустить сервер:
{% highlight bash %}
...
Success. You can now start the database server using:

    pg_ctl -D data -l logfile start
{% endhighlight %}

Для запуска мы используем более удобный для нас вариант[^pg_ctl]:
{% highlight bash %}
$ postgres -D data/
LOG:  database system was shut down at 2015-08-28 15:51:28 MSK
LOG:  MultiXact member wraparound protections are now enabled
LOG:  database system is ready to accept connections
LOG:  autovacuum launcher started
# Для остановки сервера используйте сочетание CTRL+C.
{% endhighlight %}

Теперь можно подключиться к только что созданной базе данных:
{% highlight bash %}
$ psql -d template1
psql (9.6devel)
Type "help" for help.
{% endhighlight %}

---

[^pg_ctl]: [Подсказка о запуске с помощью ```postgres -D ...``` была удалена Брюсом Момжданом в мае 2015-го года](http://git.postgresql.org/gitweb/?p=postgresql.git;a=blobdiff;f=src/bin/initdb/initdb.c;h=f1c4920ad1a1c6e981fde1a511099f3a8fbc82ea;hp=86949209b093d7b5066e2124d92272b871868a26;hb=2200713aa8bb857ae2737f659073907628c9e2ca;hpb=23c33198b961f27c80655a7cf439d49ef5a1833d)

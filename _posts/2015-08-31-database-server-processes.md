---
layout: post
title:  "Процессы сервера Postgresql"
date:   "2015-08-31 21:01:12"
---

При старте сервера создаётся несколько дочерних процессов.
Postgresql использует процессы, вместо POSIX Threads.
Брюс Момджан дал этому очень простое объяснение на одном из своих выступлений[^pgopen-momjian]:
при подключении нового клиента создаётся копия процесса ``postmaster`` с собственным изолированным стэком вызовов;
при использовании pthreads достичь такого поведения было бы сложно.

{% highlight bash %}
$ ps -u `id -nu` f | grep [p]ostgres
25879 pts/2    S+     0:00      \_ postgres -D data/
25881 ?        Ss     0:00          \_ postgres: checkpointer process
25882 ?        Ss     0:00          \_ postgres: writer process
25883 ?        Ss     0:00          \_ postgres: wal writer process
25884 ?        Ss     0:00          \_ postgres: autovacuum launcher process
25885 ?        Ss     0:00          \_ postgres: stats collector process
{% endhighlight %}

Этот список процессов не полон.
В дальнейшем мы посмотрим на другие (например, ``wal sender`` и ``archiver``).
Давайте кратко остановимся на каждом из перечисленных процессов.

## postgres

Собственно процесс ``postmaster`` &mdash; главный процесс сервера ``Postgresql``.
Он является родительским для всех остальных процессов.
Точкой входа для изучения является файл ``src/backend/postmaster/postmaster.c``.

## checkpointer process

``checkpointer process`` сбрасывает на диск &laquo;грязные&raquo; страницы с данными.
Этот процесс появился в версии ``9.2``.

Его код размещён в ``src/backend/postmaster/checkpointer.c``

## writer process

``writer process``, также известный как ``background writer (bgwriter)``, появился в версии ``8.0``.
Этот процесс срабатывает каждые ``bgwriter_delay`` секунд,
ищет в shared buffer pool'е изменённые страницы,
извлекает ``bgwriter_lru_maxpages`` и записывает их на диск.

В следующих постах мы подробнее ознакомимся с алгоритмом ``LRU`` &mdash; ``Least Recently Used`` и подобными ему алгоритмами,
а так же с фундаментальными структурами данных и их реализации в ``Postgres``.

Точкой входа является файл ``src/backend/postmaster/bgwriter.c``.

## wal writer process

``wal writer process`` появился в версии ``8.3``.
Он записывает на диск и синхронизирует ``WriteAheadLog (WAL)`` после фиксации (``COMMIT``) транзакции.

Точка входа: ``src/backend/postmaster/walwriter.c``.

## autovacuum launcher process

``autovacuum launcher process`` автоматизирует запуск команд ``VACUUM`` и ``ANALYZE``.
Он проверяет таблицы и индексы на большое количество вставленных, обновлённых или удалённых кортежей (tuples).
Подробнее про ``autovacuum`` можно прочитать в документации[^doc-autovacuum].

Точка входа находится в файле ``src/backend/postmaster/autovacuum.c``.

## stats collector process

``stats collector process`` собирает статистику по кластеру (в терминологии ``Postgresql`` кластером называется одна сущность сервера[^pg-cluster]).
Собранная статистика доступна в таблицах и представлениях, начинающихся с ``pg_stat*``.
Она помогает планировщику выбирать наиболее эффективный план запроса.

Точка входа &mdash; файл ``src/backend/postmaster/pgstat.c``.

Пока я готовил этот пост, наткнулся на короткую презентацию[^edb-slides] от EDB о процессах сервера ``Postgresql``.
Во многом пост повторяет материалы презентации. Однако в дальнейшем погружении мы посмотрим на работу этих процессов более подробно уже изнутри.

---

[^pgopen-momjian]: [Bruce Momjian: Inside PostgreSQL Shared Memory](https://youtu.be/Nwk-UfjlUn8?t=964)
[^pg-cluster]: [Creating a Database Cluster](http://www.postgresql.org/docs/devel/static/creating-cluster.html)
[^doc-autovacuum]: [The Autovacuum Daemon](http://www.postgresql.org/docs/devel/static/routine-vacuuming.html#AUTOVACUUM)
[^edb-slides]: [Overview of Postgres Utility Processes](http://www.slideshare.net/EnterpriseDB/overviewutilityprocesses-finalaug222013)

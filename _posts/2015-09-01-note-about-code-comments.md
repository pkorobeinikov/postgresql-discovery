---
layout: post
title:  "Замечание о комментариях в исходном коде"
date:   "2015-09-01 21:00:46"
---

Я являюсь противником комментариев в исходном коде и считаю,
что код должен быть таким, чтобы он был понятен без комментариев.
Как показывает практика, комментарии не поддерживаются программистами.
Они меняют поведение кода и забывают поменять комментарии вокруг
или пишут очевидные вещи, поддаваясь привычке всё комментировать
(даже если комментарии бесполезны и повторяют написанное в коде).

Приведу несколько простых примеров.

{% highlight php %}
<?php

/**
 * Класс пользователя системы
 *                 (да неужели?)
 */
class Person
{
    // приватная переменная с именем пользователя
    //           (это понятно из спецификатора видимости и имени)
    private $name;

    // Конструктор класса (и как мы не догадались?)
    public function __construct($name, $surname)
    {
        $this->name = $name;
        ...
{% endhighlight %}

{% highlight python %}
def sum(a, b):
    """
    Возвращает произведение переменных a и b.
        (заметили несоответствие?)
    """
    return a + b;
{% endhighlight %}

Подобные комментарии вводят в заблуждение.
Скорее всего и в ваших проектов полно комментариев, подобных этим.
Но в ``Postgres`` дела обстоят иначе.

Практически каждый файл снабжён заголовком, в котором описано его предназначение.
К функциям даны актуальные комментарии, описывающие их действия.
Время от времени в список рассылки ``pgsql-hackers`` приходят патчи по актуализации тех или иных комментариев.
Во многих комментариях даны ссылки на литературу по применяемым алгоритмам,
например, ``src/backend/utils/sort/tuplesort.c``:

{% highlight c %}
...
* We do not form the initial runs using Knuth's recommended replacement
* selection data structure (Algorithm 5.4.1R), because it uses a fixed
* number of records in memory at all times.  Since we are dealing with
* tuples that may vary considerably in size, we want to be able to vary
* the number of records kept in memory to ensure full utilization of the
* allowed sort memory space.  So, we keep the tuples in a variable-size
* heap, with the next record to go out at the top of the heap.  Like
* Algorithm 5.4.1R, each record is stored with the run number that it
* must go into, and we use (run number, key) as the ordering key for the
* heap.  When the run number at the top of the heap changes, we know that
* no more records of the prior run are left in the heap.
...
{% endhighlight %}

Некоторое время назад для меня оказался очень полезным комментарий о добавлении объекта в системный кэш (``src/include/utils/syscache.h``)

{% highlight c %}
/*
 *		SysCache identifiers.
 *
 *		The order of these identifiers must match the order
 *		of the entries in the array cacheinfo[] in syscache.c.
 *		Keep them in alphabetical order (renumbering only costs a
 *		backend rebuild).
 */
{% endhighlight %}

В результате мне не пришлось проводить реверс-инжиниринг всего связанного кода.
После добавления идентификатора объекта в перечисление ``SysCacheIdentifier``:

{% highlight c %}
enum SysCacheIdentifier
{
	AGGFNOID = 0,
	...
	COLLOID,
	COMPRESSORNAME, /* Идентификатор отношения с кастомными компрессорами */
	CONDEFAULT,
    ...
{% endhighlight %}

я добавил соответствующий элемент в массив ``cacheinfo``:

{% highlight c %}
...
{CompressorRelationId,		/* COMPRESSORNAME */
    /* дальнейшее определение элементов структуры cachedesc */
...
{% endhighlight %}

Подводя итог, хочу сказать, что комментарии в исходном коде ``Postgresql`` не только актуальны и поддерживаются компетентными специалистами.
Они могут служить своего рода учебником по разработке баз данных и справочником по алгоритмам и структурам.
Во время изучения исходного кода обращайте внимание на такие комментарии и при необходимости изучайте источники,
к которым они отсылют.

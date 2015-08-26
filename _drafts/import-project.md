## Импорт проекта в среду разработки

В wiki[^wiki] есть статья,
в которой описан процесс создания проекта в Eclipse CDT.

Для сборки вам понадобятся несколько пакетов

* libreadlineX-dev, где X &mdash; 5 или 6
* zlib1g-dev
* bison
* flex

Уточните, как эти пакеты называются в вашей системе и установите их.

Перед импортом проекта сконфигурируйте его.
Среды разработки на этапе создания проекта обычно делают маленькую гадость &mdash;
они пытаются запустить ```./configure``` и ```make``` без параметров.
После этого придётся выковыривать файлы проекта из всех системных каталогов руками.

{% highlight bash %}
./configure --prefix=$YOUR_WORKSPACE_PATH/postgresql.org/build  \
    --enable-debug      \
    --enable-depend     \
    --enable-cassert
{% endhighlight %}

----

[^wiki]: [Импорт проекта в Eclipse](https://wiki.postgresql.org/wiki/Working_with_Eclipse)

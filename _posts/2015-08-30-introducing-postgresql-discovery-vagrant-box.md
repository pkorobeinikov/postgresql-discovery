---
layout: post
title:  "Vagrant box с настроенным окружением"
date:   "2015-08-30 13:50:56"
---
Следуя инструкциям в предыдущих постах, нам удалось развернуть окружения для изучения внутреннего устройства ``Postgresql``.

Сейчас я хочу облегчить задачу создания воспроизводимого окружения и помочь тем, кто испытал трудности.
В этом нам помогут три инструмента:

* ``Vagrant``[^vagrant] &mdash; фронтэнд к управлению виртуальными машинами
* ``VirtualBox``[^vbox] не нуждается в представлении
* ``SaltStack``[^saltstack] &mdash; система управления конфигурацией

Если на вашей рабочей машине ещё не установлены ``Vagrant`` и ``VirtualBox`` &mdash; установите их.

Получите рабочую копию репозитория ``postgresql-discovery-vagrant-box``:

{% highlight bash %}
$ git clone https://github.com/postgresql-discovery/postgresql-discovery-vagrant-box.git
{% endhighlight %}

Затем выполните команду ``vagrant up``.
Первый запуск потребует времени. Он включает в себя следующие этапы:

1. Загрузка образа виртуальной машины (~400Мб)
2. Установка на неё системы управления конфигурацией ``SaltStack`` (выполняется автоматически)
3. Клонирование репозитория ``postgresql`` (выполняется автоматически)

Если вы неувернно чувствуете себя с ``Vagrant``, ознакомьтесь разделом документации о начале работы[^vagrant-getting-started].
Ознакомьтесь с файлом ``README.md`` репозитория ``postgresql-discovery-vagrant-box``[^readme] &mdash; в нём даны подробные инструкции по развёртыванию.

После развёртывания бокса вы получите виртуальную машину, с необходимым для сборки и установки ``Postgresql`` программным обеспечением и репозиторием ``Postgresql`` с последним коммитом в ветку ``master``.

---

[^vagrant]: [Vagrant](https://www.vagrantup.com)
[^vbox]: [VirtualBox](https://www.virtualbox.org)
[^saltstack]: [SaltStack](http://saltstack.com)
[^vagrant-getting-started]: [Vagrant Getting Started Guide](https://docs.vagrantup.com/v2/getting-started/index.html)
[^readme]: [Postgresql Discovery Vagrant Box README](https://github.com/postgresql-discovery/postgresql-discovery-vagrant-box/blob/master/README.md)

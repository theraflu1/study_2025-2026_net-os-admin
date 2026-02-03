---
## Front matter
title: "Отчёт о лабораторной работе"
subtitle: "Лабораторная работа 8"
author: "Мошаров Денис Максимович"

## Generic otions
lang: ru-RU
toc-title: "Содержание"

## Bibliography
bibliography: bib/cite.bib
csl: pandoc/csl/gost-r-7-0-5-2008-numeric.csl

## Pdf output format
toc: true # Table of contents
toc-depth: 2
lof: true # List of figures
lot: true # List of tables
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian4
  options:
	- spelling=modern
	- babelshorthands=true
polyglossia-otherlangs:
  name: english
## I18n babel
babel-lang: russian
babel-otherlangs: english
## Fonts
mainfont: IBM Plex Serif
romanfont: IBM Plex Serif
sansfont: IBM Plex Sans
monofont: IBM Plex Mono
mathfont: STIX Two Math
mainfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
romanfontoptions: Ligatures=Common,Ligatures=TeX,Scale=0.94
sansfontoptions: Ligatures=Common,Ligatures=TeX,Scale=MatchLowercase,Scale=0.94
monofontoptions: Scale=MatchLowercase,Scale=0.94,FakeStretch=0.9
mathfontoptions:
## Biblatex
biblatex: true
biblio-style: "gost-numeric"
biblatexoptions:
  - parentracker=true
  - backend=biber
  - hyperref=auto
  - language=auto
  - autolang=other*
  - citestyle=gost-numeric
## Pandoc-crossref LaTeX customization
figureTitle: "Рис."
tableTitle: "Таблица"
listingTitle: "Листинг"
lofTitle: "Список иллюстраций"
lotTitle: "Список таблиц"
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Цель работы

Приобретение практических навыков по установке и конфигурированию SMTP-сервера

# Выполнение лабораторной работы

Для начала перейдём на сервере под учётную запись root и установим postfix (рис. [-@fig:001]).

![Установка Postfix](image/1.png){#fig:001}

Далее, установим s-nail (рис. [-@fig:002]).

![Установка s-nail](image/2.png){#fig:002}

Далее добавим правило фаервола для разрешения работы smtp протокола и восстановим метки selinux, после чего включим postfix (рис. [-@fig:003]).

![Предварительная настройка](image/3.png){#fig:003}

Введём команду postconf. Она выводит список всех параметров настроек. Вывод довольно большой (рис. [-@fig:004]).

![postconf](image/4.png){#fig:004}

Посмотрим, чему равняется значение myorigin и значение mydomain. Имя домена это наше доменное имя, а myorigin это имя хоста. Поменяем его на имя домана, проверим обновление конфигурации на корректность с помощью postfix check и перезагрузим postfix (рис. [-@fig:005]).

![Обновление конфигурации postfix](image/5.png){#fig:005}

С помощью postfix -n выведем конфигурации, которые не соответствуют конфигурациям по умолчанию (рис. [-@fig:006]).

![postfix -n](image/6.png){#fig:006}

Захардкодим имя домена как наш домен. После этого посмотрим на то, какие протоколы интернет допустимы. Как видим, все. Сделаем так, чтобы допустим был только ipv4 и перезагрузим postfix (рис. [-@fig:007]).

![Обновление настроек протоколов](image/7.png){#fig:007}

Попробуем от имени своей учётной записи отправить себе же письмо (рис. [-@fig:008]).

![Отправка письма](image/8.png){#fig:008}

Откроем в другой вкладке лог, который считывается из файла /var/log/maillog. Как видим, почтовое сообщение было доставлено. Об этом можно судить по сообщению, которое идёт перед последним и гласит "delivered tp mailbox" (рис. [-@fig:009]).

![Лог почты](image/9.png){#fig:009}

Убедимся также, что в папке /var/spool/mail создались файлы наших пользователей (рис. [-@fig:010]).

![/var/spool/mail](image/10.png){#fig:010}

Перейдём в клиентскую машину, зайдём под рутом и тоже установим postfix (рис. [-@fig:011]).

![Установка Postfix](image/11.png){#fig:011}

Также установим s-nail (рис. [-@fig:012]).

![установка s-nail](image/12.png){#fig:012}

На клиенте, ровно так же, как мы делали на сервере, включим только ipv4 в качестве рабочего протокола и перезагрузим postfix (рис. [-@fig:013]).

![Настройка протоколов на клиенте](image/13.png){#fig:013}

Теперь отправим с клиента письмо на сервер (рис. [-@fig:014]).

![Отправка письма](image/14.png){#fig:014}

На сервере мы ничего нового не увидим (рис. [-@fig:015]).

![Логи почты](image/15.png){#fig:015}

Позволим другим устройствам в сети отправлять почту серверу. Для этого поменяем параметр inet_interfaces с localhost на all, а также добавим в mynetworks нашу локальную сеть 192.168.0.0, после чего перезагрузим postfix (рис. [-@fig:016]).

![Включение получчения писем из локальной сети](image/16.png){#fig:016}

Попробуем на клиенте снова отправить письмо (рис. [-@fig:017]).

![Повторная отправка письма](image/17.png){#fig:017}

Теперь, как мы видим, появилось новое письмо. Как видим, в предпоследней строке сказано, что оно успешно доставлено (delivered to mailbox) (рис. [-@fig:018]).

![Успешная отправка письма согласно логам](image/18.png){#fig:018}

Теперь отправим письмо не на конкретное устройство, а к пользователю (рис. [-@fig:019]).

![Отправка письма пользователю](image/19.png){#fig:019}

Как видим, наше письмо не доставилось, потому что нет информации о каком-либо почтовом сервере (No route to host) (рис. [-@fig:020]).

![Неуспешная доставка письма](image/20.png){#fig:020}

Мы увидим информацию об этом зависшем письме через команду postqueue -p (рис. [-@fig:021]).

![postqueue -p](image/21.png){#fig:021}

Теперь поменяем файлы прямой и обратной зоны для добавления информации о почтовом сервере. В файле прямой зоны будут такие изменения (добавление записи о почтовом сервере) (рис. [-@fig:022]).

![Файл прямой зоны](image/22.png){#fig:022}

А в файле обратной зоны будут такие изменения (рис. [-@fig:023]).

![Файл обратной зоны](image/23.png){#fig:023}

Обновим список пунктов назначения, включив туда сервер, после чего перезагрузим postfix и обновив метки selinux, а так же перезагрузив dns сервер, после чего введём postqueue -f, чтобы выгрузить нашу очередь сообщений (рис. [-@fig:024]).

![Обновление списков назначения почты](image/24.png){#fig:024}

Теперь мы увидим, что наше письмо было доставлено (рис. [-@fig:025]).

![Лог об успешно доставленом письме](image/25.png){#fig:025}

Теперь сохраним конфигурацию для vagrant с сервера, а также создадим файл mail.sh (рис. [-@fig:026]).

![Сохранение конфигурации](image/26.png){#fig:026}

В файл mail.sh напишем следующий скрипт (рис. [-@fig:027]).

![mail.sh](image/27.png){#fig:027}

Теперь переключимся на клиент, где также создадим файл mail.sh для vagrant (рис. [-@fig:028]).

![Создание скрипта mail.sh для клиента](image/28.png){#fig:028}

Скрипт для клиента будет выглядеть так (рис. [-@fig:029]).

![mail.sh для клиента](image/29.png){#fig:029}

Теперь зайдём в vagrantfile и добавим запуск этих скриптов для клиента и сервера (рис. [-@fig:029]).

![vagrantfile](image/30.png){#fig:030}

# Выводы

В результате выполнения лабораторной работы были получены навыки настройки smtp сервера
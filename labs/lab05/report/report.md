---
## Front matter
title: "Отчёт о лабораторной работе"
subtitle: "Лабораторная работа 5"
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

Приобретение практических навыков по расширенному конфигурированию HTTP-сервера Apache в части безопасности и возможности использования PHP.

# Выполнение лабораторной работы

Для начала запустим наш сервер через vagrant (рис. \@fig:001).

![Запуск сервера](image/1.png){#fig:001}

Далее нам необходимо создать сертификат. Создадим его в созданной нами папке /etc/pki/tls/private, который сделаем симлинком на папку /etc/ssl/private. После создания сертификата, скопируем его в /etc/ssl/certs (рис. [-@fig:002]).

![Создание сертификата](image/2.png){#fig:002}

Перейдём в /etc/httpd/conf.d и отредактируем конфигурационный файл www.dmmosharov.net.conf (рис. [-@fig:003]).

![Редактирование конфигурационного файла httpd](image/3.png){#fig:003}

Изменим его на следующее содержание, добавив поддержку https. Прокомментируем каждую строку (рис. [-@fig:004]).: 

1: Начало блока конфигурации виртуального хоста, который будет обрабатывать запросы на всех IP-адресах (*) по порту 80 (стандартный порт для HTTP).  
2: Указание контактного email-адреса администратора сайта, который может отображаться на страницах с ошибками.  
3: Определение корневой директории (/var/www/html/www.dmmosharov.net), где расположены файлы сайта.  
4: Установка основного доменного имени, по которому будет доступен сайт.  
5: Задание альтернативных имен (псевдонимов) для этого же хоста. Здесь дублирует основное имя.  
6: Указание пути к файлу для записи логов ошибок (журнала ошибок).  
7: Указание пути к файлу для записи логов доступа (журнала посещений) в стандартном формате common.  
8: Включение модуля mod_rewrite для преобразования URL-адресов.  
9: Правило, которое перенаправляет все HTTP-запросы на их HTTPS-аналоги с кодом 301 (постоянное перенаправление).  
10: Завершение блока конфигурации виртуального хоста для порта 80.  
11: Начало условного блока: конфигурация внутри будет применяться только если загружен модуль mod_ssl.  
12: Начало блока конфигурации виртуального хоста для обработки запросов по порту 443 (стандартный порт для HTTPS).  
13: Включение поддержки SSL/TLS шифрования для данного виртуального хоста.  
14: Указание email-адреса администратора для этого хоста.  
15: Определение корневой директории с файлами сайта для HTTPS-версии.  
16: Установка основного доменного имени для HTTPS-хоста.  
17: Задание альтернативных имен для HTTPS-хоста.  
18: Указание пути к файлу для логов ошибок HTTPS-хоста.  
19: Указание пути к файлу для логов доступа HTTPS-хоста.  
20: Указание пути к файлу публичного SSL-сертификата (.crt).  
21: Указание пути к файлу приватного (секретного) ключа SSL (.key).  
22: Завершение блока конфигурации виртуального хоста для порта 443.  
23: Завершение условного блока IfModule.  

![www.dmmosharov.net.conf](image/4.png){#fig:004}

Теперь с помощью фаервола разрешим работу с https и пеезапустим службу httpd (рис. [-@fig:005]).

![Настройка фаервола](image/5.png){#fig:005}

Теперь перейдём на машину клиента. Попробуем войти на наш сайт www.dmmosharov.net и увидим, что браузер предупреждает о том, что соединение незащищено. Тем не менее, подтверждаем переход на сайт (рис. [-@fig:006]).

![Предупреждение](image/6.png){#fig:006}

Как видим, соединение к сайту происходит по https (рис. [-@fig:007]).

![Подключение к сайту](image/7.png){#fig:007}

Посмотрим на сертификат, и увидим, что там те же данные, что мы вводили при создании (рис. [-@fig:008]).

![Сертификат](image/8.png){#fig:008}

Установим на сервере пакет php (рис. [-@fig:009]).

![Установка php](image/9.png){#fig:009}

Создадим на замену старого index.html файл index.php в папке /var/www/html/www.dmmosharov.net (рис. [-@fig:010]).

![Замена файла index.html на index.php](image/10.png){#fig:010}

В index.php запишем следующее (рис. [-@fig:011]).

![Содержимое index.php](image/11.png){#fig:011}

Теперь восстановим метки selinux и поменяем владельца нашей папки /var/www на apache, после чего перезапустим службу (рис. [-@fig:012]).

![Исправление настроек доступа](image/12.png){#fig:012}

Теперь посмотрим, как на машине клиента отображается наш сайт (рис. [-@fig:013]).

![Отображение сайта](image/13.png){#fig:013}

Теперь сохраним нашу конфигурацию для vagrant (рис. [-@fig:014]).

![Сохранение конфигурации](image/14.png){#fig:014}

И модифицируем наш файл /vagrant/provision/server/http.sh таким образом, что он будем устанавливать пакет php и настраивать firewall для работы с https (рис. [-@fig:015]).

![http.sh](image/15.png){#fig:015}

# Выводы

В результате выполнения лабораторной работы были получены навыки по конфигурированию HTTP-сервера Apache и https
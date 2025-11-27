---
## Front matter
title: "Лабораторная работа №5"
subtitle: "Кибербезопасность предприятия"
author: 

  - НКНбд-01-22;
  - Аристид Жан,
  - Акопян Сатеник,
  - Кадров Виктор,
  - Петров Артем,
  - Нве Манге Хосе Херсон Мико,
  - Эспиноса Висилита Кристина Микаела,
  
  - НПИбд-01-22;
  - Стариков Данила,

  - НФИбд-02-22;
  - Кадрова(Чемоданова) Ангелина

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
fontsize: 12pt
linestretch: 1.5
papersize: a4
documentclass: scrreprt
## I18n polyglossia
polyglossia-lang:
  name: russian
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
lolTitle: "Листинги"
## Misc options
indent: true
header-includes:
  - \usepackage{indentfirst}
  - \usepackage{float} # keep figures where there are in the text
  - \floatplacement{figure}{H} # keep figures where there are in the text
---

# Описание сценария

Во внутреннем сегменте организации необходимо получить доступ к DNS-серверу и найти флаг в одной из DNS-записей[@scen]. 

Для прохождения данного сценария в первую очередь потребуется активная meterpreter-сессия с узлом в сегменте DMZ. 

Вариант получения meterpreter-сессии с корпоративным сайтом с помощью модуля wp_wpdiscuz_unauthenticated_file_upload представлен на скриншотах(рис. [-@fig:001] - рис. [-@fig:002]). 

![Параметры модуля wp_wpdiscuz_unauthenticated_file_upload](image/1-options-img.png){#fig:001 width=90%}

![Параметры модуля wp_wpdiscuz_unauthenticated_file_upload](image/3-run-meterpreter-session-img.png){#fig:002 width=90%}

Вариант получения meterpreter-сессии с почтовым сервером с помощью модуля exchange_proxyshell_rce представлен на скриншотах(рис. [-@fig:003] - рис. [-@fig:004]). 

![Параметры модуля exchange_proxyshell_rce](image/4-proxyshell-options.png){#fig:003 width=90%}

![Настройка и запуск meterpreter-сессии с почтовым сервером с помощью exchange_proxyshell_rce ](image/5-set-proxyshell-patams-and-run.png){#fig:004 width=90%}

# Способы получения флага

В данном разделе описаны способы получения флага.

## Поиск DNS-сервера

После получения сессии можно переходить к процедуре поиска нужного сервера. В первую очередь выполнить проброс портов во внутреннююсеть с помощью команды autoroute и запустить данную сеть (рис. [-@fig:005]) – run autoroute -s 10.10.10.0/24.

![Регистрация подсети во фреймворке Metasploit](image/6-set-autoroute.png){#fig:005 width=90%}

Свернуть сессию (команда bg) и с помощью модуля multi/gather/ping_sweep сканировать внутреннюю сеть организации для выбора адреса, который может быть использован для дальнейшей атаки. Провести поиск DNS-сервера (рис. [-@fig:006]). 

![Маршрут до внутренней сети](image/7-ping-sweep-img.png){#fig:006 width=90%}

С помощью команды route распечатать маршруты, которые обнаружены Metasploit(рис. [-@fig:007]). 

Далее вернуться в сессию с почтовым сервером (sessions {N}) и отобразить хосты, которые находятся во внутренней сети организации(рис. [-@fig:007]).

![Маршруты. Таблица маршрутизации на хосте](image/8-route-and-arp-on-WEB_SERVER_NOT_MS_EXHCANGE.png){#fig:007 width=90%}

Проверить наличие открытых портов на хостах, которые находятся во внутренней сети организации, с помощью модуля nmap. Поскольку сканируемые хосты находятся во внутренней сети, в первую очередь необходимо настроить прокси, через который будут проходить все запросы при сканировании. Для этого можно использовать модуль metasploit auxiliary/server/socks_proxy. 

Свернуть сессию (команда bg), далее найти и выбрать модуль metasploit auxiliary/server/socks_proxy(рис. [-@fig:008]). 

![Поиск и выбор модуля metasploit auxiliary/server/socks_proxy](image/9-use-proxy.png){#fig:008 width=90%}

Настроить модуль в соответствии с параметрами, которые указаны в конфигурационном файле /etc/proxychains.conf(рис. [-@fig:009]- рис. [-@fig:010]).

![ Параметры в конфигурационном файле /etc/proxychains.conf](image/10-check-proxy-params.png){#fig:009 width=90%}

![Настройки и запуск модуля](image/11-set-proxy-params-and-start.png){#fig:010 width=90%}

Далее открыть новый терминал kali. В новом терминале запустить сканирование 100 самых часто используемых портов с помощью команды proxychains nmap –n –sT –Pn --top-ports 100 {IP}. 

В результате сканирования будет получен список открытых портов(рис. [-@fig:011]). 

![Результаты сканирования портов](image/12-nmap.png){#fig:011 width=90%}

По стандарту RFC 1035 все DNS-серверы отвечают на порту 53 TCP и UDP. По результатам сканирования можно сделать вывод, что узел 10.10.10.15 является целью атаки – DNS-сервером с открытым 22 портом SSH(рис. [-@fig:012]).

![Идентификация DNS-сервера](image/13-img-port-22-reachable.jpg){#fig:012 width=90%}

## Bruteforce пароля

В результате сканирования будет получен список открытых портов, в котором обнаружен 22 порт, используемый по умолчанию для подключения по протоколу SSH. 

Для реализации атаки перебором паролей использовать словарь rockyou.txt, который находится по пути /usr/share/wordlists/(рис. [-@fig:013]).

![Местонахождение словаря rockyou.txt](image/14-worldlists-ls.png){#fig:013 width=90%}

Логин пользователя можно получить с помощью файла userlist в директории /usr/share/wordlists с именами пользователей. Выбрать 
пользователя «user», далее запустить утилиту hydra с помощью команды proxychains hydra -V -f -l user -P rockyou.txt -t 32 10.10.10.15 
ssh(рис. [-@fig:014] - рис. [-@fig:015]).

![Запуск атаки перебором](image/15-hydra.png){#fig:014 width=90%}

![Результат атаки перебором](image/16-password-found.png){#fig:015 width=90%}

Для получения доступа к DNS-серверу можно воспользоваться подключением по SSH по полученным учетным данным или модулем metasploit auxiliary/scanner/ssh/ssh_login с указанием параметров для входа. 

Подключение по SSH по полученным учетным данным осуществляется с помощью команды proxychains ssh user@10.10.10.15(рис. [-@fig:016]). Далее необходимо ввести найденный пароль. 

![Успешное подключение по SSH](image/17-login.png){#fig:016 width=90%}

Для получения флага необходимо вывести содержимое файла /etc/hosts с помощью команды cat /etc/hosts(рис. [-@fig:017]).

![Просмотр флага](image/18-cat-hosts.png){#fig:017 width=90%}

Альтернативным вариантом является создание shell-сессии с помощью модуля auxiliary(scanner/ssh/ssh_login). Настройки модуля приведены на скриншоте(рис. [-@fig:018]). После настроек необходимо активировать открытую сессию с помощью команды sessions {N}, где N – номер созданной сессии. 

![Подключение с помощью модуля](image/19-ssh-login-tool.png){#fig:018 width=90%}

После получения сессии необходимо вывести содержимое файла hosts с помощью команды cat(рис. [-@fig:019]). 

![Просмотр флага](image/20-cat-hosts.png){#fig:019 width=90%}

# Вывод

В ходе выполнения данной лабораторной работы мы выполнили тренировку “Захват DNS-сервера”. В процессе выполнения работы освоили практические навыки выявления, анализа и атаки уязвимостей в различных системах.

# Список литературы{.unnumbered}

::: {#refs}
:::


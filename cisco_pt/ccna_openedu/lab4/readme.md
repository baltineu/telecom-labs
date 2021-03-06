# Дисклеймер:
Неформальный стиль данного мануала объясняется тем, что он был ориентирован на студентов-сокурсников.

# Предварительные настройки
Итак, топология. Наверное, у вас будет другая, в таком случае надеюсь, что этот мануал всё же будет вам полезен:

![Топология](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/topology4.PNG)

Оранжевая часть – там, где между роутерами должна быть OSPF-маршрутизация.

Зеленая – EIGRP.

Голубой компьютер в левой нижней части и зеленый в правой – находятся в отдельных виланах.

```
Содержание:
[Предварительные настройки]
[Настройка OSPF]
[Настройка EIGRP]
[Настройка статической маршрутизации между EIGRP и OSPF]
[Балансировка трафика в EIGRP]
```

Между верхними роутерами проходит serial-кабель. Для начала добавим на верхних роутерах модуль WIC-1T, для этого тыкнем по ним и перейдём на вкладочку Physical. Здесь обязательно предварительно отключить роутеры, поскольку вставлять модуль под напряжением («на горячее») строго запрещается, красной стрелкой показано, куда нужно тыкнуть, чтобы отключить.

После этого вставляем наш модуль.

![2](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/2.png)

Настройки верхних роутеров:

![3](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/3.png)

![4](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/4.png)

Несколько пояснений по командам:

[```hostname```] – нужна просто, чтобы поменять имя роутера в командной строке. Это нужно было мне при создании мануала, чтобы лучше показать материал, но в принципе не обязательно его менять в вашей работе. 

[```int se 0/3/0```] – для тех, кто к четвертой УТП только вышел из запоя или вылез из танка :D  Так мы заходим на интерфейс для его настройки. Чтобы выйти можно написать [exit].

[```clock rate```] – эта штука по сути настраивает пропускную способность. Serial-кабели тем и прекрасны, что можно настроить какую хотите скорость передачи данных. Подробнее читайте тут: <http://ciscotips.ru/clockrate> (очень советую заглянуть из-за важных команд, там написанных). Я лишь обращу ваше внимание, что подключение выполняется при помощи вот такого кабеля:

![5](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/5.png)

Какой из роутеров вы подключите первым будет DCE, на нём вы и выполняете команду clock rate. 

[```ip address```] – это мы выдаём айпишники. В моём примере я решил использовать 223.0.0.0 сеть для связи между роутерами. У левого роутера айпишник 223.0.0.1, у правого 223.0.0.2. Также вы можете использовать линки, что это такое читайте в «Сетях для самых маленьких»:

><https://linkmeup.ru/blog/14.html> - ctrl+F и поиск по слову "линк" - о линковых сетях и бесклассовой IP-адресации

Но в данном примере для простоты будем использовать сети с маской 255.255.255.0

Не забудьте включить [```se 0/3/0```] интерфейсы на обоих роутерах командой [```no shutdown```]


Перейдём к настройке сегментов между роутерами.

> Сегмент сети – участок сети, устройства которого делят общую полосу пропускания.

В качестве сети между роутерами использую 222.0.1.0 

Верхний из них будет иметь в этом сегменте IP 222.0.1.1

![6](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/6.png)

А нижний соответственно 222.0.1.2

![7](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/7.png)

Аналогично будут настраиваться и остальные сети.

Тут я вспомнил, что для полного построения топологии нам необходимо, чтобы на средних и нижнем роутерах было по три интерфейса FastEthernet.

На уже включенном и настроенном OSPF2 произвожу такие действия:

![8](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/8.png)

[```copy running-config startup-config```] – все настройки роутера, как и свитча, хранятся в файлах. ```Running-config``` отвечает за текущие настройки. А ```startup-config``` – за настройки, которые автоматически восстановятся после перезагрузки устройства. Командой copy мы копируем текущие настройки в файл с «несгораемыми» настройками. Теперь при внезапном отключении мы не потеряем все плоды наших трудов. Хорошо бы так делать на всех устройствах, лишним точно не будет.

Вот теперь можно выключить OSPF2 и вставить в него наш модуль с FastEthernet. Аналогично поступим с OSPF3 – если уже успели на нём настроить сетки, то сохранитесь, как выше.

![9](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/9.png)

![10](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/10.png)

Включаемся и продолжаем настройку.

Дальше всё в принципе видно на скриншотах.

![11](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/11.png)

![12](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/12.png)

![13](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/13.png)

В нижний роутер тоже вставляем NM-1FE-TX.

![14](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/14.png)

Смотря на самый первый рисунок в документе, настройте аналогично вышеизложенным примерам сеточки.
Снизу можете посмотреть какие конкретно, но можете настроить и свои.
Роутеру OSPF4, предварительно сохранив конфигурацию, тоже добавьте модуль NM-1FE-TX.

![15](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/15.png)

Теперь осталось настроить то, что снизу.

![16](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/16.png)

Заходим на OSPF4 и делаем так:

![17](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/17.png)

[```int fa 1/0.1```] – заходим на первый сабинтерфейс fa 1/0

[```encapsulation dot1q 1```] – настройка приёма тэгированного трафика с первого vlan’a.

[```ip address 192.168.1.1 255.255.255.0```] – установление айпи-адреса для первого сабинтерфейса.

```int fa 1/0.2``` – аналогично.

Подробнее про это:

<https://linkmeup.ru/blog/14.html>  - «Сети для самых маленьких», InterVLAN Routing.

(1-й УТП).

А далее настройка Switch0:

![18](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/18.png)

Fa0/2 свитча0 должен принимать трафик от PC1 и помечать его меткой первого вилана. Далее он передаёт его на fa0/24 по trunk’у, который этот тэг вилана сохраняет. На OSPF4 роутер видит, например, ip назначения 192.168.2.2 (это PC0). Он помечает пакет меткой второго вилана и передаёт его на fa1/0.2. Далее наш пакет опять по trunk’у приходит на свитч. Свитч снимает метку второго вилана и отдаёт пакетик PC0 по fa0/1.

Пришло время настроить свитч.

![19](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/19.png)

[```switchport mode trunk```] – многие могут спросить, почему без указания конкретных виланов далее. Дело в том, что так мы обозначаем разрешение всем пакетам любых виланов проходить через данный порт без снятия тэга.

![20](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/20.png)

[```vlan 2```] – создание на свитче соответствующего вилана. Во втором утп вы наверное разобрались с VTP и передачей виланов. 

[```name net2```] – даём вилану имя. 

![21](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/22.png)

[```int fa 0/1```]

[```switchport mode access```] – переводим порт в режим установления метки какого-то вилана.

[```switchport access vlan 2```] – говорим, какой именно меткой помечать.

![22](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/23.png)

Аналогично.

Если что, как-то так настраивается dg (default gateway) для компьютера:

![23](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/24.png)

В общем, вот что у нас в итоге получилось:

![24](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/25.png)

Я больше не буду утруждать вас столь подробными объяснениями. Правую часть сетки настроите сами. Если что, ниже привожу свои настройки всех сетей топологии. Займёмся после всего этого динамической маршрутизацией! Наконец-то! Ура! 

Кстати.

Вот как можно спрятать или вернуть порты.

![25](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/25.png)

![26](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/26.png)

Штука полезная. Вернёмся к топологии. В правой части нужно всем роутерам добавить модуль с FastEthernet. А на нижний роутер даже с двумя портами. Он называется NM-2FE2W. Не забудьте сохранить настройки верхнего, [```copy run st```] (Команды можно сокращать, если что. Я тут уже неоднократно этим пользовался). Если что-то забыли, листайте наверх.
Если вы дали айпишник не тому интерфейсу, зайдите на него и введите [```no ip addr```]. Слово «no» вообще позволяет отменить действие любой команды, главное указать точно, какой.

Для проверки пользуйтесь [```sh run```] (show running-config)

Некоторые скриншоты с места событий, чтобы вы если что сверились:

![27](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/27.png)

![28](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/28.png)

![29](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/29.png)

![30](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/30.png)

![31](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/31.png)

Советую соединять между собой одинаково называющиеся порты, т.к. это очень удобно. Чтобы когда они накладываются, понять, где какой, просто смотришь на порт соседа:

![32](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/32.png)

Вот что в итоге получилось:

![33](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/33.png)

# Настройка OSPF:

Вот что в принципе нужно усвоить из теории, чтобы сделать лабу:

- Что такое метрика и чем она отличается от административного расстояния.

> Метрика – расстояние от точки сети, в которой в данный момент находится пакет, до сети назначения. Вычисляется метрика на основе различных параметров (число промежуточных маршрутизаторов, пропускная способность, надежность канала, задержка и т.п.) в зависимости от используемого протокола маршрутизации.

> Административное расстояние (Administrative Distance, AD) – целое число от 0 до 255, показывающее приоритет источника маршрута. Маршрут с более низким AD считается более надежным. Таким образом, административное расстояние – степень доверия к источнику, предоставившему информацию о маршруте. Примером ситуации, в которой административное расстояние играет существенную роль, является ситуация, когда информация об одной и той же сети назначения предоставляется двумя и более протоколами маршрутизации (будет выбран маршрут, предоставленный протоколом с наименьшим административным расстоянием)

- Простая математическая дробь: 

![34](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/34.png)

Из которой видно, что чем выше пропускная способность (bandwidth), тем ниже метрика. (OSPF)

- И как это дело настраивать

![35](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/35.png)

Всё! Вперёд и с песней, проходимся по всем роутерам и запускаем процесс ospf для наших сеточек между роутерами.

![36](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/36.png)

У верхнего роутера не вносим в процесс сеть 223.0.0.0, так как по заданию у нас там статическая маршрутизация.

Первая настройка:

![37](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/37.png)

Самое время объяснить, что такое wildcard маска.

В видео курса дана обтекаемая формулировка, что в ней могут даже чередоваться нули и единицы. Почему – читайте тут: <https://habr.com/ru/post/131712/>

Мы же будем ~~стрелять из пушки по воробьям~~ пользоваться ей как обычной маской.

Была у нас маска 255.255.255.0, обратная ей 0.0.0.255.

И в OSPF при настройке как раз используется обратная.

Продолжим настройку:

![38](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/38.png)

Здесь у меня не совсем прямые руки, разумеется, достаточно один раз прописать нужную команду.

На нижнем роутере обязательно добавляем в процесс OSPF нижние сеточки, идущие до компьютеров. Таким образом все роутеры выше нижнего узнают об этих сеточках и построят маршруты в них. (Ошибка: вместо 224 в сети используйте 222). 

![39](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/39.png)

Давайте на последнем оставшемся роутере немного поэкспериментируем. Вообще-то мы могли сразу писать вот так:

![40](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/40.png)

То есть такая запись wildcard означает, что 2 октета справа могут меняться как они хотят.

Благодаря этому мы запустили процесс на всех сеточках, которые начинаются с 222.0 и смогли достучаться до роутеров-соседей. Подробнее про тот вывод в командной строке, который мы наблюдаем, да и вообще про OSPF смотрите в «Сетях для самых маленьких»: <https://www.youtube.com/watch?v=8zxdW6ag8Us&t=5s>

OSPF настроен, можете попробовать попинговать роутеры в OSPF с компьютеров. Только не забудьте перейти в Realtime из симуляции :D Многие люди забывали это и не сдавали утп.

Доказано на опыте, что если сразу всё настроить правильно, всё заработает. Это утп приходилось переделывать не раз и не два, но поверьте, оно того стоит. 

Если всё ещё ничего не работает, посмотрите как сделано в «Сетях для самых маленьких».

Loopback-интерфейсы и Router-ID действительно важны, я и сам этого не понимал, пока у меня при написании этих строк внезапно не отказался работать OSPF :D

Если что, вас всегда спасёт [```no router ospf 1```]. Дальше просто пишите всё заново.

В [```show ip route```] должны быть маршруты от OSPF, обозначенные буквой O. Если вы наблюдаете только маршруты C (connected), настройте OSPF заново.

# Настройка EIGRP:
- В теории EIGRP достаточно сложен и многогранен. Но на практике в этой лабе мы должны знать, что метрика будет высчитываться как:

![41](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/41.png)

Где:

![42](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/42.png)

То есть просто суммируются два показателя, которые вы можете посмотреть для любого интерфейса командой [```show interface```]

![43](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/43.png)

BW – пропускная способность, DLY – задержка.

- Настраиваться будет так:

![44](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/44.png)

Здесь уже можно указывать wildcard, можно не указывать.

Поехали!

![45](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/45.png)

![46](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/46.png)

![47](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/47.png)

![48](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/48.png)

![49](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/49.png)

Обязательное уточнение:

Когда настраиваете EIGRP, на всех пишите [```no auto-summary```].

Почему – хорошо написано в «Сетях для самых маленьких»

><https://linkmeup.ru/blog/33.html> (ctrl+f автоматическое суммирование)

Вот в общем-то и всё. Теперь можно попинговать какой-нибудь роутер с компов.

Если будут проблемы, советы те же:

[```sh ip route```] – должны быть маршруты с буквой D

[```no router eigrp 1```] – удалить процесс маршрутизации, чтобы запустить заново.

Кстати, мельком заметил тут отличную команду на <http://xgu.ru/wiki/OSPF_в_Cisco>

![50](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/50.png)

# Настройка статической маршрутизации между EIGRP и OSPF:
Представим ситуацию. Пакетику нужно дойти с левого нижнего компьютера до правого нижнего.

![51](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/51.png)

После красного крестика пакетик никуда не пойдёт, потому что роутер OSPF4 понятия не имеет о том, где расположена сеть 192.168.3.0 и кому дальше передавать пакетик для её достижения.

При этом в правой половине всем роутерам EIGRP прекрасно известно, что такое сеть 192.168.3.0

Эта проблема решится, если и в левой половине все роутеры узнают об этой сети.

1) Добавить маршрут на левом верхнем роутере OSPF1
2) Сделать так, чтобы он рассказал о нём всем роутерам OSPF

Сделать то же самое для сети 192.168.4.0

**1) Добавление маршрута**

![52](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/52.png)

223.0.0.2 – так называемый ip next hop – айпи адрес интерфейса se0/3/0 роутера EIGRP1, которому нужно передать пакеты, идущие в сети 192.168.3.0 и 4.0.

255.255.255.0 – маска этих сетей

**2) Рассказать всем о своих маршрутах**

![53](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/53.png)

Готово, благодаря этой команде наш OSPF1 передаст информацию о маршрутах другим роутерам OSPF.

Вот что теперь можно увидеть, например, на OSPF4 [```sh ip route```]:

![54](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/54.png)

Как вы наверное уже догадались, нужно то же самое настроить для EIGRP.

**3) EIGRP. Добавление маршрута**

![55](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/55.png)

**4) EIGRP. Рассказать всем**

![56](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/56.png)

Как видите, команды немного отличаются.

В EIGRP: [```redistribute static```]

В OSPF: [```redistribute static subnets```]

Это происходит из-за того, что OSPF – классовый протокол, а EIGRP – бесклассовый. В EIGRP мы по умолчанию настраиваем передачу именно подсетей, а в OSPF требуется уточнение, иначе он будет передавать сети по правилам для классов IPv4.

Подробнее про OSPF и EIGRP: <https://linkmeup.ru/blog/33.html>

На этом про настройку этой топологии у меня всё.

# Балансировка трафика в EIGRP
Если иное не было настроено, метрика маршрута в EIGRP рассчитывается по формуле:

>(B+D)\*256

Где B – Bandwidth, пропускная способность, рассчитывается по формуле:

>B = 10^7/(минимальная_пропускная_способность_по_дороге_маршрута_в_килобитах);

D – Delay, сумма задержек интерфейсов, из которых *выходит* пакет по дороге маршрута **в десятках микросекунд** (команда [```show interface se0/3/0```] выводит значение в микросекундах).

Для примера далее рассматривается маршрут от роутера EIGRP4 до сети 223.0.0.0/24 (сеть между роутерами OSPF1 и EIGRP1). Соответствующего маршрута нет в таблице EIGRP4, поскольку на EIGRP1 не включена редистрибуция напрямую подключенных сетей. Последовательность команд для её включения:
```
EIGRP1(config)#router eigrp 1
EIGRP1(config-router)#redistribute connected metric ?
<1-4294967295> Bandwidth metric in Kbits per second
EIGRP1(config-router)#redistribute connected metric 128 ?
<0-4294967295> EIGRP delay metric, in 10 microsecond units
EIGRP1(config-router)#redistribute connected metric 128 2000 ?
<0-255> EIGRP reliability metric where 255 is 100% reliable
EIGRP1(config-router)#redistribute connected metric 128 2000 255 ?
<1-255> EIGRP Effective bandwidth metric (Loading) where 255 is 100% loaded
EIGRP1(config-router)#redistribute connected metric 128 2000 255 1 ?
<1-65535> EIGRP MTU of the path
EIGRP1(config-router)#redistribute connected metric 128 2000 255 1 1500
```
Значения пропускной способности, задержки, надежности, загрузки и MTU взяты стандартные.
На EIGRP4 ```show ip route``` показывает один маршрут до сети 223.0.0.0:
```
D EX 223.0.0.0/24 [170/20514560] via 221.0.2.1, 00:00:10, FastEthernet1/0
```
C метрикой 20514650. И в таблице топологий (```show ip eigrp topology```) этот маршрут тоже только один:
```
P 223.0.0.0/24, 1 successors, FD is 20514560 via Rstatic (20514560/20512000)
```
Так происходит потому, что возможные пути через EIGRP2 и EIGRP3 имеют AD не лучше, чем FD через EIGRP1 у EIGRP4. Подробнее: https://linkmeup.ru/blog/1195/

>AD – Advertised Distance – Для роутера это метрика маршрута до нужной сети от роутера-соседа, который прислал информацию об этом маршруте.

>FD – Feasible Distance – Для роутера это метрика маршрута до нужной сети от него самого.

Однако очевидно, что существует ещё два возможных пути, через EIGRP2 и EIGRP3, которые могут быть использованы. Задержка на пути через них будет ровно на 100 мкс больше задержки пути через EIGRP1 – поскольку в этих путях ровно на один интерфейс больше.
Таким образом, чтобы в таблице маршрутизации появились эти пути, нужно установить значение задержки на интерфейсе fa1/0 EIGRP4 равное 200 мкс. Последовательность команд (значение указывается в десятках микросекунд):
```
Router(config)#int fa 1/0
Router(config-if)#delay 20
```
Теперь команда ```show ip route``` показывает три равноценных маршрута до сети 223.0.0.0/24 и пакеты будут идти по трем разным путям.
```
…
D EX 223.0.0.0/24 [170/20517120] via 221.0.6.1, 00:00:46, FastEthernet0/1
[170/20517120] via 221.0.5.1, 00:00:46, FastEthernet0/0
[170/20517120] via 221.0.2.1, 00:00:44, FastEthernet1/0
```
>P.S. Значение задержки на интерфейсе не сохраняется (даже при сохранении конфига ```copy run st```, похоже на глюк CPT 2019), нужно писать каждый раз заново. Пинг до 223.0.0.1 (или 223.0.0.2, если из OSPF-домена) для проверки правильности настройки нужно запускать из терминала компьютера, иначе подставится неверный IP.

![57](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab4/57.png)

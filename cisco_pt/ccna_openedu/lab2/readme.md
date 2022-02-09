![Топология](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/topology2.PNG)

# Цели:
Повторить:

1) Понятие VLAN +
1) Понятие trunk, структура кадра 802.1Q +
1) Создание VLANs, конфигурирование access ports +
1) Роль протокола VTP +
1) Конфигурирование trunk

Для приведенной топологии настроить:

1) 5 VLAN: 3 слева от роутера и 2 справа. 
1) При помощи настройки trunk allowed запретить компьютерам во VLAN 1 взаимодействовать с роутером и другими VLAN, как следствие.
# Ход работы (повторение):
**1. VLAN**

Виртуальная локальная сеть (VLAN). 

Default/native vlan 1. (не тэгируется).

IEEE 802.1Q – в кадре eth после da и sa появляется поле tag.

Компьютеры самостоятельно не маркируют трафик. Этим занимается коммутатор, пришедший кадр он маркирует согласно правилу switchport access vlan такой-то.

Если кадр идёт с сабинтерфейса роутера, где настроена соответствующая инкапсуляция vlan – кадр уже промаркирован. В нем есть поля TCI и TPID.

С портов доступа (access ports) к конечным устройствам кадры выходят без меток (коммутатор снимает метку перед передачей).

Магистральное соединение – trunk (метки не снимаются). 
**2. Trunk (с.180)**

VLANs также используются для ускорения работы STP.

Можно создавать сети без trunk’ов, только с access-портами, как на рисунке. Тогда для передачи кадров одной VLAN между коммутаторами потребуется отдельное физическое подключение (по одному интерфейсу каждого коммутатора).

![VLAN](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.1_VLAN.png)

Схема работает, но плохо масштабируется (для 20 VLAN потребуется 20 портов с каждой стороны). 

Есть два протокола для реализации trunk (изменяющих обычный Ethernet, добавляя туда метки). Старый протокол от Cisco - ISL (Inter-Switch Link) и более новый 802.1Q, которым пользуется большинство производителей, включая Cisco. Диапазон VLAN в обоих -–1-4094, для меток используется 12 бит с двумя зарезервированными значениями (0 и 4095). Этот диапазон дробится на два: обычный 1-1005 и расширенный 1006-4094, только некоторые коммутаторы могут использовать последний. 

![802.1Q](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.2_802.1Q.png)

![eth802.1q](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.3_eth802.1q.png)

По умолчанию vlan 1 – native vlan. У native vlan есть важная особенность – с его помощью можно передавать пакеты коммутаторы, не поддерживающему 802.1q. Метка 802.1q просто не добавляется к пакетам vlan 1.

В итоге trunk – это одно общее подключение (обычно между коммутаторами, либо между коммутатором и маршрутизатором), по которому передаются тэгированные пакеты разных VLAN.  

**3. Примеры конфигурирования (с. 185)**

Первый пример.

![1st example](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.4_1st_example.png)

Создание VLAN и конфигурирование портов доступа (access ports).
Последовательность действий следующая:

1) В режиме глобальной конфигурации создать VLAN.

```
Switch>en
Switch#conf t
Enter configuration commands, one per line. End with CNTL/Z.
Switch(config)#vlan 2
Switch(config-vlan)#name new\_vlan
Switch(config-vlan)#exit
```

2) На каждом порте доступа настроить, для какого VLAN будет выставляться/сниматься метка. Также можно настроить режим работы порта – switchport mode access – чтобы данный порт не передавал подключенному устройству пакеты других VLAN с меткой. (Важно: похоже, возможность прописать снятие метки для определенного vlan, при возможности работы порта в режиме trunk, необходимо для администрирования коммутаторов в рамках конкретного VLAN, т.е. коммутатор для определенного VLAN может выступать конечным устройством, для которого нужно снять метку?)

```
Switch(config)#int fa 0/1
Switch(config-if)#switchport access vlan 2
Switch(config-if)#int fa0/2
Switch(config-if)#switchport access vlan 2
```

С interface-range:

```
Switch(config)#int range fa0/3-15
Switch(config-if-range)#switchport access vlan 2
Switch(config-if-range)#end
```

Вывести информацию о принадлежности интерфейсов к VLANs.

![cmd1](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.5_cmd1.png)

Вывести информацию о конкретном VLAN, его названии и номерах его интерфейсов.

![cmd2](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.6_cmd2.png)

Второй пример.

![2nd example](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.7_2nd_example.png)

В данном примере используется 6 портов коммутатора, каждый из которых является портом доступа (подключен к конечному устройству). Тэгированные пакеты одного VLAN не должны передаваться коммутатором в другие VLAN. Для этого необходимо использовать команду switchport mode access, упомянутую ранее. Данная команда отключает DTP протокол (Dynamic Trunking Protocol), который используется для установления trunk-соединения.

```
Switch>en
Switch#conf t
Switch(config)#int range fa0/11-12
Switch(config-if-range)#switchport access vlan 1
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#int range fa0/13-14
Switch(config-if-range)#switchport access vlan 2
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#int range fa0/15-16
Switch(config-if-range)#switchport access vlan 3
% Access VLAN does not exist. Creating vlan 3
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#exit
```

![cmd3](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.8_cmd3.png)

Как итог использования switchport mode access – коммутатор точно не отправит ARP-пакет из, допустим, порта VLAN 1 в порт VLAN 3, на котором без этого внезапно могла оказаться настройка switchport mode trunk, и конечное устройство из VLAN 3 смогло бы перехватить этот пакет.

**4. Роль VTP (с. 190)**

VTP больше нет в CCNA (в 200/301), есть в CCNP.

Все примеры в Cert. guide используют коммутаторы с отключенным VTP (каким-либо способом, например через vtp mode transparent или vtp mode off). 

VTP нужен, чтобы изменения конфигурации (создание новых VLAN или изменение параметров старых) на одном коммутаторе передавать на другие коммутаторы посредством широковещательной рассылки (но только между ними есть trunk-соединение). Инициировать рассылку может только vtp-server.

При помощи команды show vtp status можно узнать роль данного коммутатора в VTP. Если он является клиентом или сервером, то:

\- Server может конфигурировать VLAN только внутри стандартного диапазона 1-1005

\- Client не может конфигурировать VLAN

\- И Server, и Client могут узнавать о новых VLAN от других коммутаторов и удалять имеющиеся у себя VLAN из-за их удаления на других коммутаторах

\- sh run не покажет конфигурацию vlan, в данном случае нужно использовать другие команды sh

**5. Конфигурирование trunk (с. 191)**

switchport mode trunk – создать VLAN trunk, который переправляет пакеты любых VLAN. Помимо этой команды есть множество опций, например: 

1) можно настроить тип trunking, это может быть IEEE 802.1Q, ISL, либо коммутаторы могут прийти к соглашению, какой именно тип использовать, при помощи DTP;
2) можно включить administrative mode, в котором есть возможность настроить на коммутаторе, всегда ли делать trunk, никогда ли, либо определять совместно с другим коммутатором, создавать trunk или нет.

switchport trunk encapsulation (dot1q | isl | negotiate) – подкоманда интерфейса, определяющая используемый протокол (negotiate – если оба коммутатора поддерживают оба протокола, они используют ISL, иначе используют тот, который есть у обоих). В Cisco Packet Tracer у коммутатора 2960 данная команда отсутствует, но есть похожая роутерная команда:

encapsulation dot1q <vlan> - вводится на сабинтерфейсе и определяет протокол и метку vlan сабинтерфейса.

Административный режим (administrative mode) – позволяет коммутаторам взаимодействовать, чтобы динамически создавать trunk-соединения и выбирать протокол для этого соединения.

Рассмотрим топологию:
  
![trunk](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.9_trunk.png)

Преднастроены vlan 1 на всех компьютерах слева, vlan 2 – сверху, vlan 3 – справа.

На верхнем и нижних коммутаторах включен режим dynamic auto на интерфейсе gi0/1:

```
Switch(config)#int gi0/1
Switch(config-if)#switchport mode dynamic auto
```
![trunk2](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.10_trunk2.png)
  
Пакеты vlan 3 не проходят из верхней части в нижнюю, поскольку в dynamic auto режиме оба коммутатора ждут получения DTP от другого коммутатора. Пусть на одном из коммутаторов интерфейс перевели в режим dynamic desirable:

```
Switch(config)#int gi0/1
Switch(config-if)#switchport mode dynamic desirable
```
![trunk3](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.11_trunk3.png)

В этом случае arp и пинг из верхней части vlan 3 начинает проходить в нижнюю. Оба коммутатора настроили trunk по dot1q, поскольку 2960 поддерживает только dot1q.

![trunk4](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.12_trunk4.png)
  
![trunk5](https://github.com/baltineu/telecom-labs/blob/main/cisco_pt/ccna_openedu/sources/lab2/2.13_trunk5.png)

Стоит заметить, что если на одном коммутаторе настроен switchport mode trunk, а на другом – switchport mode dynamic auto, trunk-соединение будет создано через обмен DTP-пакетами. В целях безопасности рекомендуется отключать этот обмен командой switchport nonegotiate.

Также рекомендуется не использовать режим access с одной стороны, trunk с другой.

Далее приводится таблица, в которой столбцами и строками являются заданные на коммутаторах режимы, а в ячейках указан тип создаваемого соединения для этой комбинации.

|Админ. режим|Access|Dyn. Auto |Trunk|Dyn. Desirable|
| :- | :- | :- | :- | :- |
|access |access|access|Do not use|Access|
|Dyn. auto|access|access|trunk|Trunk|
|trunk|Do not use|trunk|trunk|Trunk|
|Dyn. desirable|access|trunk|trunk|trunk|
  
# Практика:

Компьютерам настроить IP-адреса, шлюзы. На коммутаторах настроить access-порты. Настроить trunk порты. Отключить vtp (vtp mode transparent). На роутере настроить subinterfaces. Нативному vlan (1) предоставить основной интерфейс, для остальных подинтерфейсы, на каждом подинтерфейсе включить encapsulation dot1q <vlan>. Обязательно настроить trunk между коммутатором и роутером.

Чтобы отрезать vlan 1 от внешнего мира между Switch2 и Switch3 настроить trunk-соединение таким образом, что switchport trunk allowed vlan 2-3. 

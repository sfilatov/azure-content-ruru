
<properties
   pageTitle="Пиринговая связь между виртуальными сетями Azure | Microsoft Azure"
   description="Сведения о пиринговой связи между виртуальными сетями в Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="NarayanAnnamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2016"
   ms.author="narayan" />

# Пиринговая связь между виртуальными сетями

Пиринговая связь между виртуальными сетями — это механизм подключения между двумя виртуальными сетями, находящимися в одном регионе, через магистральную сеть Azure. После создания пиринговой связи две виртуальные сети выглядят как одна при любом подключении. Они по-прежнему управляются как отдельные ресурсы, но теперь находящиеся в них виртуальные машины могут взаимодействовать друг с другом напрямую по частным IP-адресам.

Трафик между виртуальными машинами в таких виртуальных сетях направляется через инфраструктуру Azure, подобно трафику между виртуальными машинами в одной виртуальной сети. Некоторые преимущества пиринговой связи между виртуальными сетями:

- подключение между ресурсами в разных виртуальных сетях, характеризующееся низкой задержкой и высокой пропускной способностью;
- возможность использовать ресурсы, такие как сетевые устройства и VPN-шлюзы, в качестве транзитных точек в пиринговой виртуальной сети;
- возможность подключения виртуальной сети, в которой используется модель Azure Resource Manager, к виртуальной сети, в которой используется классическая модель развертывания, с обеспечением полной связи между ресурсами в этих виртуальных сетях.

Требования и ключевые аспекты, касающиеся пиринговой связи между виртуальными сетями:

- Две виртуальные сети, между которыми настраивается пиринговая связь, должны находиться в одном регионе Azure.
- Для настройки пиринговой связи пространства IP-адресов используемых виртуальных сетей не должны перекрываться.
- Пиринговая связь устанавливается между двумя виртуальными сетями. При этом возникновение производной переходной связи не предполагается. Например, если для виртуальной сети A установлена пиринговая связь с виртуальной сетью B, а для виртуальной сети B — с виртуальной сетью C, эта связь не преобразуется в пиринговую связь между сетями A и C.
- Пиринговую связь можно установить между виртуальными сетями в двух разных подписках, если ее разрешает привилегированный пользователь обеих подписок.
- Для виртуальной сети, в которой используется модель развертывания Resource Manager, можно установить пиринговую связь с другой виртуальной сетью, в которой используется та же модель, или с виртуальной сетью, в которой используется классическая модель развертывания. Однако между двумя виртуальными сетями с классической моделью развертывания пиринговую связь установить невозможно.
- Для обмена данными между виртуальными машинами в пиринговых виртуальных сетях нет дополнительных ограничений пропускной способности, однако объем передаваемого трафика ограничивается размером виртуальной машины.


![Базовая пиринговая связь между виртуальными сетями](./media/virtual-networks-peering-overview/figure01.png)

## Соединение
После создания пиринговой связи между двумя виртуальными сетями виртуальная машина (с веб-ролью или рабочей ролью) в одной из этих сетей может напрямую подключаться к другим виртуальным машинам в пиринговой виртуальной сети. Между этими двумя сетями поддерживается полное подключение на уровне IP-адресов.

Задержки в сети при круговом пути между двумя виртуальными машинами в пиринговых виртуальных сетях такие же, как и в локальной виртуальной сети. Пропускная способность сети зависит от пропускной способности виртуальной машины, которая пропорциональна ее размеру. Дополнительные ограничения пропускной способности не применяются.

Трафик между виртуальными машинами в пиринговых виртуальных сетях передается напрямую через серверную инфраструктуру Azure, а не через шлюз.

Виртуальные машины в виртуальной сети могут получать доступ к конечным точкам с балансировкой нагрузки, которые находятся в пиринговой виртуальной сети. Чтобы заблокировать доступ к другим виртуальным сетям или подсетям, в любой виртуальной сети можно использовать группы безопасности сети (NSG).

При настройке пиринговой связи пользователи могут включать или отключать правила группы безопасности сети, регулирующие связь между виртуальными сетями. Если пользователь разрешит полное подключение между пиринговыми виртуальными сетями (параметр по умолчанию), он сможет применять группы безопасности сети к определенным подсетям или виртуальным машинам, чтобы заблокировать или запретить доступ к ним.

Внутреннее разрешение DNS-имен Azure не поддерживается для виртуальных машин в пиринговых виртуальных сетях. Виртуальным машинам назначаются внутренние DNS-имена, разрешимые только в пределах локальной виртуальной сети. Тем не менее пользователи могут настроить виртуальные машины в пиринговых виртуальных сетях в качестве DNS-серверов для виртуальной сети.

## Цепочка служб
Пользователи могут настроить определяемые пользователем таблицы маршрутизации, указывающие, какие виртуальные машины в пиринговых виртуальных сетях используются для IP-адреса следующего прыжка (как показано на схеме далее в этой статье). Таким образом пользователи могут создать цепочку служб для передачи трафика из одной виртуальной сети на виртуальное устройство, запущенное в пиринговой виртуальной сети, через определяемые пользователем таблицы маршрутизации.

Кроме того, можно создавать среды со звездообразным подключением, в которых в центральной виртуальной сети ("звезде") размещаются инфраструктурные компоненты, например виртуальное сетевое устройство. Все остальные виртуальные сети ("лучи") подключаются к этой сети по пиринговой связи, и часть трафика направляется на устройства, работающие в центральной виртуальной сети. Если вкратце, в определяемой пользователем таблице маршрутизации можно указать для следующего прыжка IP-адрес виртуальной машины, входящей в пиринговую виртуальную сеть.

## Использование шлюзов для локального подключения
Для каждой виртуальной сети, независимо от наличия пиринговой связи с другой виртуальной сетью, можно настроить собственный шлюз, который будет использоваться для подключения к локальной сети. С помощью шлюза также можно настроить подключение [между виртуальными сетями](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), даже если для них установлена пиринговая связь.

Если для виртуальных сетей настроены оба типа подключения, трафик между виртуальными сетями проходит через пиринговую связь (т. е. через магистральную сеть Azure).

Если между виртуальными сетями установлена пиринговая связь, пользователи могут использовать шлюз в одной из этих сетей в качестве транзитного пункта при подключении к локальной сети. В этом случае виртуальная сеть, использующая удаленный шлюз, не может иметь свой собственный. У одной виртуальной сети может быть только один шлюз. Это может быть локальный или удаленный шлюз (в пиринговой виртуальной сети), как показано на следующем рисунке.

Транзит через шлюз не поддерживается между виртуальной сетью с классической моделью развертывания и виртуальной сетью с моделью развертывания Resource Manager. Он поддерживается, только если в обеих пиринговых виртуальных сетях используется модель Resource Manager.

Трафик между пиринговыми виртуальными сетями, которые используют одно подключение Azure ExpressRoute, передается по пиринговой связи (т. е. через магистральную сеть Azure). Пользователи по-прежнему могут использовать в каждой виртуальной сети локальные шлюзы для подключения к локальному каналу или настроить транзит через общий шлюз для локального подключения.

![Транзит при пиринговой связи между виртуальными сетями](./media/virtual-networks-peering-overview/figure02.png)

## Подготовка
Пиринговую связь между виртуальными сетями может настроить только привилегированный пользователь. Это отдельная функция в рамках пространства имен виртуальной сети. Пользователю можно предоставить права на авторизацию пиринга. Пользователь, имеющий доступ для чтения и записи к виртуальной сети, автоматически наследует эти права.

Администратор и привилегированный пользователь с правами на настройку пиринговой связи могут инициировать пиринговую связь с другой виртуальной сетью. Если на другой стороне есть такой же запрос и другие требования выполняются, пиринговая связь устанавливается.

Дополнительные сведения о настройке пиринговой связи между двумя виртуальными сетями см. в других статьях, указанных в разделе "Дальнейшие действия".

## Ограничения
Существуют ограничения на число пиринговых связей в одной виртуальной сети. Дополнительные сведения см. в разделе [Ограничения сети](../azure-subscription-service-limits.md#networking-limits).

## Цены
В течение ознакомительного периода пиринговую связь между виртуальными сетями можно использовать бесплатно. После выпуска общедоступной версии за входящий и исходящий трафик по пиринговой связи будет взиматься номинальная плата. Дополнительные сведения см. на странице с [ценами](https://azure.microsoft.com/pricing/details/virtual-network).


## Дальнейшие действия
- [Настройка пиринга виртуальных сетей с помощью портала Azure](virtual-networks-create-vnetpeering-arm-portal.md).
- Дополнительные сведения о [группах безопасности сети](virtual-networks-nsg.md).
- Дополнительные сведения об [определяемых пользователем маршрутах и IP-пересылке](virtual-networks-udr-overview.md).

<!---HONumber=AcomDC_0824_2016-->
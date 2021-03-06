---
title: Разрешение имен для виртуальных машин и экземпляров ролей
description: 'Сценарии разрешения имен для Azure IaaS, гибридных решений, между различными облачными службами, Active Directory и с помощью DNS-сервера '
services: virtual-network
documentationcenter: na
author: GarethBradshawMSFT
manager: carmonm
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2016
ms.author: telmos

---
# Разрешение имен для ВМ и экземпляров ролей
В зависимости от того, как вы используете Azure для размещения IaaS, PaaS и гибридных решений, вам может понадобиться разрешить созданным виртуальным машинам и экземплярам ролей взаимодействовать друг с другом. Хотя это взаимодействие может осуществляться с помощью IP-адресов, гораздо проще использовать имена узлов, которые легко запоминаются и не меняются.

Если для экземпляров ролей и виртуальных машин, размещенных в Azure, требуется разрешение доменных имен на доступ к внутренним IP-адресах, это можно сделать с помощью одного из двух методов:

* [разрешение имен Azure;](#azure-provided-name-resolution)
* [разрешение имен с помощью собственного DNS-сервера](#name-resolution-using-your-own-dns-server) (при этом запросы могут перенаправляться на DNS-серверы, предоставляемые Azure).

Тип разрешения имен зависит от способа взаимодействия виртуальных машин и экземпляров ролей друг с другом.

**В следующей таблице представлены сценарии и соответствующие решения для разрешения имен.**

| **Сценарий** | **Решение** | **Суффикс** |
| --- | --- | --- |
| Разрешение имен между экземплярами ролей или виртуальными машинами, расположенными в одной облачной службе или виртуальной сети |[разрешение имен Azure;](#azure-provided-name-resolution) |имя узла или полное доменное имя |
| Разрешение имен между экземплярами ролей или виртуальными машинами, расположенными в разных виртуальных сетях |Управляемые клиентами DNS-серверы, перенаправляющие запросы между виртуальными сетями для разрешения в Azure (DNS-прокси). См. раздел [Разрешение имен с помощью собственного DNS-сервера](#name-resolution-using-your-own-dns-server) |только полное доменное имя |
| Разрешение имен локального компьютера и службы из экземпляров роли или виртуальных машин в Azure |Управляемые клиентами DNS-серверы (например: локальный контроллер домена, локальный контроллер домена только для чтения или сервер DNS, вторично синхронизированный с помощью передач зоны). См. [Разрешение имен с помощью собственного DNS-сервера](#name-resolution-using-your-own-dns-server) |только полное доменное имя |
| Разрешение имен узлов Azure с локальных компьютеров |Перенаправление запросов на управляемый клиентом DNS прокси-сервер в соответствующей сети, прокси-сервер перенаправляет запросы в Azure для разрешения. См. [Разрешение имен с помощью собственного DNS-сервера](#name-resolution-using-your-own-dns-server) |только полное доменное имя |
| Обратный поиск DNS для внутренних IP-адресов |[разрешение имен с помощью собственного DNS-сервера.](#name-resolution-using-your-own-dns-server) |Недоступно |
| Разрешение имен между виртуальными машинами или экземплярами ролей, расположенными не в виртуальной сети, а в различных облачных службах |Не применяется Подключение между виртуальными машинами и экземплярами ролей в разных облачных службах не поддерживается за пределами виртуальной сети. |Недоступно |

## разрешение имен Azure;
Вместе с разрешением общедоступных DNS-имен Azure предоставляет разрешение внутренних имен для виртуальных машин и экземпляров ролей, которые находятся в той же виртуальной сети или облачной службе. Виртуальные машины или экземпляры в облачной службе используют один и тот же DNS-суффикс, поэтому достаточно будет только имени узла. Но в классических виртуальных сетях разные облачные службы имеют разные DNS-суффиксы, поэтому для разрешения имен между разными облачными службами требуется полное доменное имя. В виртуальных сетях с моделью развертывания на основе Resource Manager DNS-суффикс является одинаковым для всей виртуальной сети (поэтому полное доменное имя не требуется). DNS-имена можно назначать как сетевым картам, так и виртуальным машинам. Несмотря на то что разрешение имен Azure не требует настройки, это не самый лучший выбор для всех сценариев развертывания, как показано в приведенной выше таблице.

> [!NOTE]
> Что касается веб-ролей и рабочих ролей, вы можете также получать доступ ко внутренним IP-адресам экземпляров ролей на основе имени роли и номера экземпляра с помощью REST API управления службами Azure. Дополнительные сведения см. в статье [Справочник по REST API управления службами ](https://msdn.microsoft.com/library/azure/ee460799.aspx).
> 
> 

### Функции и рекомендации
**Функции**

* Простота использования: для использования разрешения имен, предоставляемого Azure, дополнительные настройки не нужны.
* Служба разрешения имен Azure отличается высокой доступностью, что устраняет необходимость создавать кластеры DNS-серверов и управлять ими.
* Может использоваться в сочетании с DNS-серверами для разрешения имен узлов в локальной среде и Azure.
* Предоставляется разрешение имен между экземплярами ролей или виртуальными машинами, расположенными в одной облачной службе, без необходимости указывать полное доменное имя.
* Разрешение имен предоставляется между виртуальными машинами в виртуальных сетях, использующих модель развертывания на основе Resource Manager, без необходимости указывать полное доменное имя. В виртуальных сетях с классической моделью развертывания полное доменное имя требуется при разрешении имен в разных облачных службах.
* Вместо использования автоматически создаваемых имен узлов вы можете создавать имена, которые наиболее точно описывают ваши развертывания.

**Рекомендации**

* Созданный Azure DNS-суффикс нельзя изменить.
* Собственные записи нельзя регистрировать вручную.
* WINS и NetBIOS не поддерживаются. (В проводнике Windows виртуальные машины не отображаются.)
* Имена узлов должны быть DNS-совместимыми (они могут содержать только символы 0–9, a–z и «-» и не могут начинаться или заканчиваться на «-». См. раздел 2 RFC 3696.)
* Трафик запросов DNS регулируется для каждой виртуальной машины. Это не должно влиять на большинство приложений. Если наблюдается регулирование запросов, проверьте, включено ли кэширование на стороне клиента. Дополнительные сведения см. в разделе [Наиболее эффективное использование разрешения имен Azure](#Getting-the-most-from-Azure-provided-name-resolution).
* Для всех классических виртуальных сетей, использующих классическую модель развертывания, зарегистрированы только те виртуальные машины, которые находятся в первых 180 облачных службах. Это не относится к виртуальным сетям с моделями развертывания на основе Resource Manager.

### Наиболее эффективное использование разрешения имен Azure
**Кэширование на стороне клиента**

Не каждый запрос DNS должен отправляться по сети. Кэширование на стороне клиента помогает уменьшить задержку и повысить устойчивость к нестабильной работе сети, разрешая повторяющиеся запросы DNS из локального кэша. Записи DNS содержат сведения о сроке жизни (TTL). Это позволяет кэшу хранить записи максимально долго, не влияя на их актуальность. Следовательно, кэширование на стороне клиента подходит для большинства ситуаций.

По умолчанию DNS-клиент Windows имеет встроенный кэш DNS. В некоторых дистрибутивах Linux возможность кэширования не включена по умолчанию. Поэтому рекомендуется включать ее на каждой виртуальной машине под управлением Linux (после проверки на отсутствие локального кэша).

Существует несколько разных доступных пакетов кэширования DNS, включая dnsmasq. Ниже описаны шаги по установке пакета dnsmasq на наиболее распространенные версии ОС.

* **Ubuntu (используется пакет resolvconf)**:
  * просто установите пакет dnsmasq (sudo apt-get install dnsmasq).
* **SUSE (используется пакет netconf)**:
  * установите пакет dnsmasq (sudo zypper install dnsmasq);
  * активируйте службу dnsmasq (systemctl enable dnsmasq.service);
  * запустите службу dnsmasq (systemctl start dnsmasq.service);
  * измените путь /etc/sysconfig/network/config и замените блок NETCONFIG\_DNS\_FORWARDER="" текстом dnsmasq;
  * укажите в файле resolv.conf (netconfig update) кэш в качестве локального сопоставителя DNS.
* **OpenLogic (используется пакет NetworkManager)**:
  * установите пакет dnsmasq (sudo yum install dnsmasq);
  * активируйте службу dnsmasq (systemctl enable dnsmasq.service);
  * запустите службу dnsmasq (systemctl start dnsmasq.service);
  * добавьте строку prepend domain-name-servers 127.0.0.1; в файл /etc/dhclient-eth0.conf;
  * перезапустите сетевую службу (service network restart), чтобы указать кэш в качестве локального сопоставителя DNS.

> [!NOTE]
> Пакет dnsmasq — один из многих доступных кэшей DNS для Linux. Перед его использованием проверьте, соответствует ли он вашим потребностям, а также не установлен ли у вас другой кэш.
> 
> 

**Повторные попытки на стороне клиента**

DNS использует преимущественно протокол UDP. Поскольку протокол UDP не гарантирует доставку сообщений, логика повторных попыток обрабатывается в самом протоколе DNS. Каждый DNS-клиент (ОС) может реализовывать разную логику повторных попыток в зависимости от предпочтений создателей.

* ОС Windows выполняет повторную попытку через 1 с, затем через 2 и 4 с, а затем снова через 4 с.
* Повторные попытки в ОС Linux по умолчанию выполняются через 5 с. Рекомендуется изменить этот параметр, чтобы повторные попытки выполнялись 5 раз с интервалом в 1 с.

Чтобы проверить текущие параметры на виртуальной машине под управлением ОС Linux, введите команду cat /etc/resolv.conf и просмотрите строку options:

    options timeout:1 attempts:5

Файл resolv.conf обычно создается автоматически; его не следует изменять. Шаги по добавлению строки options отличаются в разных дистрибутивах.

* **Ubuntu** (используется пакет resolvconf):
  * добавьте строку options в /etc/resolveconf/resolv.conf.d/head;
  * выполните resolvconf -u для обновления.
* **SUSE** (используется пакет netconf):
  * добавьте параметр timeout:1 attempts:5 в блок NETCONFIG\_DNS\_RESOLVER\_OPTIONS="" в /etc/sysconfig/network/config;
  * выполните netconfig update для обновления.
* **OpenLogic** (используется пакет NetworkManager):
  * добавьте echo "options timeout:1 attempts:5" в /etc/NetworkManager/dispatcher.d/11-dhclient;
  * выполните service network restart для обновления.

## Разрешение имен с помощью собственного DNS-сервера
Существует множество ситуаций, в которых ваши потребности по разрешению имен могут выйти за рамки функций, предоставляемых Azure, например, при использовании доменов Active Directory или при необходимости разрешения DNS между виртуальными сетями. Для этих сценариев Azure предоставляет возможность использовать ваши собственные DNS-серверы.

DNS-серверы в виртуальной сети могут перенаправлять запросы DNS рекурсивным разрешителям Azure для разрешения имен узлов в этой виртуальной сети. Например, контроллер домена, запущенный в Azure, может отвечать на запросы DNS для своих доменов и перенаправлять все остальные запросы в Azure. Это позволяет виртуальным машинам видеть локальные ресурсы (с помощью контроллера домена) и имена узлов, предоставленные Azure (с помощью сервера перенаправления). Доступ к рекурсивным разрешителям Azure предоставляется через виртуальный IP-адрес 168.63.129.16.

Перенаправление запросов DNS также позволяет выполнять разрешение DNS между виртуальными сетями и позволяет вашим локальным компьютерам выполнять разрешение имен узлов, предоставляемых Azure. Чтобы разрешить имя узла виртуальной машины, DNS-сервер виртуальной машины должен находиться в той же виртуальной сети и быть настроен на перенаправление запросов имен узла в Azure. Так как DNS-суффиксы в разных виртуальных сетях различаются, можно использовать правила условного перенаправления для отправки запросов DNS в правильную виртуальную сеть для разрешения. На следующем рисунке показаны две виртуальные сети и локальная сеть, выполняющая разрешение DNS-имен между виртуальными сетями с помощью этого метода. Пример DNS-сервера пересылки доступен в [коллекции шаблонов быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) и на сайте [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![Разрешение имен DNS между виртуальными сетями](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

При использовании разрешения имен Azure внутренний DNS-суффикс (*.internal.cloudapp.net) передается на каждую виртуальную машину с помощью протокола DHCP. Это обеспечивает разрешение имен узлов как записей имени узла в зоне internal.cloudapp.net. При использовании собственного решения для разрешения имен этот DNS-суффикс не передается на виртуальные машины, так как влияет на другие архитектуры DNS (например, на схему присоединения к доменам). Вместо этого мы предоставляем фиктивный заполнитель (reddog.microsoft.com).

При необходимости внутренний DNS-суффикс можно определить с помощью PowerShell или API.

* Для виртуальных сетей, использующих модели развертывания с помощью Resource Manager, суффикс можно определить с помощью ресурса [сетевой карты](https://msdn.microsoft.com/library/azure/mt163668.aspx) или командлета [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx).
* Для моделей классического развертывания суффикс можно определить с помощью вызова [API получения развертывания](https://msdn.microsoft.com/library/azure/ee460804.aspx) или командлета [Get-AzureVM -Debug](https://msdn.microsoft.com/library/azure/dn495236.aspx).

Если перенаправление запросов в Azure не соответствует вашим требованиям, необходимо предоставить собственное решение DNS. Ваше решение DNS должно:

* Предоставлять корректное разрешение имен, например, с помощью [DDNS](virtual-networks-name-resolution-ddns.md). Обратите внимание, что при использовании DDNS может потребоваться отключить очистку записей DNS, так как время аренды DHCP в Azure очень длинное и очистка может удалить записи DNS преждевременно.
* Предоставлять корректное рекурсивное разрешение для разрешения внешних доменных имен.
* Быть доступным (по протоколам TCP и UDP на порту 53) для клиентов, которых оно обслуживает, и иметь доступ к Интернету.
* Быть защищенным от доступа из Интернета для снижения угроз, исходящих от внешних агентов.

> [!NOTE]
> Если в качестве DNS-серверов используются виртуальные машины Azure, для оптимальной производительности следует отключить IPv6 и назначить [общедоступный IP-адрес уровня экземпляра](virtual-networks-instance-level-public-ip.md) каждой виртуальной машине, выполняющей роль DNS-сервера. Если вы решили использовать Windows Server в качестве DNS-сервера, ознакомьтесь с приведенными [в этой статье](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) сведениями об анализе производительности и оптимизациях.
> 
> 

### Указание DNS-серверов
При использовании ваших DNS-серверов Azure предоставляет возможность указать несколько серверов для каждой виртуальной сети или каждого сетевого интерфейса (Resource Manager) либо облачной службы (классическая модель). DNS-серверы, указанные для облачной службы или сетевой карты, имеют более высокий приоритет по сравнению с серверами, указанными для виртуальной сети.

> [!NOTE]
> Свойства сетевых подключений, такие как IP-адреса DNS-серверов, не следует изменять непосредственно в виртуальной машине Windows, так как они могут быть удалены во время исправления службы, когда производится замена виртуального сетевого адаптера.
> 
> 

При использовании модели развертывания с помощью Resource Manager DNS-серверы можно указать на портале, используя API или шаблоны ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) либо PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [nic](https://msdn.microsoft.com/library/mt619370.aspx)).

При использовании классической модели развертывания DNS-серверы для виртуальной сети можно указать на портале или в [файле *конфигурации сети*](https://msdn.microsoft.com/library/azure/jj157100). Для облачных служб DNS-серверы задаются с помощью [файла *конфигурации службы*](https://msdn.microsoft.com/library/azure/ee758710) или PowerShell ([New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [!NOTE]
> Если изменить параметры DNS для уже развернутой виртуальной машины или виртуальной сети, потребуется перезапустить каждую затронутую виртуальную машину, чтобы изменения вступили в силу.
> 
> 

## Дальнейшие действия
Модель развертывания Resource Manager:

* [Создание или обновление виртуальной сети](https://msdn.microsoft.com/library/azure/mt163661.aspx)
* [Создание или обновление сетевой карты](https://msdn.microsoft.com/library/azure/mt163668.aspx)
* [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
* [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

Классическая модель развертывания:

* [Схема конфигурации службы Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Схема конфигурации виртуальной сети](https://msdn.microsoft.com/library/azure/jj157100)
* [Настройка виртуальной сети с помощью файла конфигурации сети](virtual-networks-using-network-configuration-file.md)

<!---HONumber=AcomDC_0907_2016-->
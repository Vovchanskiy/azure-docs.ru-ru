---
title: 'Azure AD Connect: порты | Microsoft Docs'
description: Эта страница является страницей технического справочника по портам, которые должны быть открыты для Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: billmath

---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Порты и протоколы, необходимые для гибридной идентификации
Следующий документ представляет собой технический справочник, содержащий сведения о портах и протоколах, которые необходимы для реализации решения для гибридной идентификации. Используйте приведенный ниже рисунок и соответствующую таблицу.

![Что такое Azure AD Connect?](./media/active-directory-aadconnect-ports/required1.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Таблица 1. Azure AD Connect и локальная служба AD
В этой таблице описываются порты и протоколы, необходимые для взаимодействия между сервером Azure AD Connect и локальной службой AD.

| Протокол | порты; | Описание |
| --- | --- | --- |
| DNS |53 (TCP или UDP) |Поиски DNS в лесу назначения. |
| Kerberos |88 (TCP или UDP) |Проверка подлинности Kerberos для леса AD. |
| MS-RPC |135 (TCP или UDP) |Используется во время начальной настройки мастера Azure AD Connect, когда он выполняет привязку к лесу AD. |
| LDAP |389 (TCP или UDP) |Используется для импорта данных из AD. Данные шифруются с помощью функции подписи и запечатывания Kerberos. |
| LDAP или SSL |636 (TCP или UDP) |Используется для импорта данных из AD. Передача данных подписывается и шифруется. Используется только при применении SSL. |
| RPC |49152- 65535 (произвольные верхние порты RPC) (TCP или UDP) |Используется во время начальной настройки мастера Azure AD Connect, когда он выполняет привязку к лесам AD. Дополнительную информацию см. в статьях базы знаний [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017) и [KB224196](https://support.microsoft.com/kb/224196). |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Таблица 2. Azure AD Connect и Azure AD
В этой таблице описываются порты и протоколы, которые необходимы для взаимодействия между сервером Azure AD Connect и Azure AD.

| Протокол | порты; | Описание |
| --- | --- | --- |
| HTTP |80 (TCP или UDP) |Используется для загрузки CRL (списки отзыва сертификатов) для проверки сертификатов SSL. |
| HTTPS |443 (TCP или UDP) |Используется для синхронизации с Azure AD. |

Список URL-адресов и IP-адресов, которые необходимо открыть в брандмауэре, см. в статье [URL-адреса и диапазоны IP-адресов Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-federation-servers/wap"></a>Таблица 3. Azure AD Connect и серверы федерации и WAP
В этой таблице описываются порты и протоколы, необходимые для взаимодействия между сервером Azure AD Connect и серверами федерации и WAP.  

| Протокол | порты; | Описание |
| --- | --- | --- |
| HTTP |80 (TCP или UDP) |Используется для загрузки CRL (списки отзыва сертификатов) для проверки сертификатов SSL. |
| HTTPS |443 (TCP или UDP) |Используется для синхронизации с Azure AD. |
| WinRM |5985 |Прослушиватель WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Таблица 4. Серверы WAP и серверы федерации
В этой таблице описываются порты и протоколы, необходимые для взаимодействия между серверами федерации и серверами WAP.

| Протокол | порты; | Описание |
| --- | --- | --- |
| HTTPS |443 (TCP или UDP) |Используется для проверки подлинности. |

## <a name="table-5---wap-and-users"></a>Таблица 5. WAP и пользователи
В этой таблице описываются порты и протоколы, необходимые для взаимодействия между пользователями и серверами WAP.

| Протокол | порты; | Описание |
| --- | --- | --- |
| HTTPS |443 (TCP или UDP) |Используется для проверки подлинности устройств. |
| TCP |49443 (TCP) |Используется для проверки подлинности с помощью сертификата. |

## <a name="table-6a-&-6b---azure-ad-connect-health-agent-for-(ad-fs/sync)-and-azure-ad"></a>Таблица 6а и 6б. Агент Azure AD Connect Health для (AD FS и синхронизации) и Azure AD
В следующих таблицах описываются конечные точки, порты и протоколы, необходимые для взаимодействия между агентами Azure AD Connect Health и Azure AD.

### <a name="table-6a---ports-and-protocols-for-azure-ad-connect-health-agent-for-(ad-fs/sync)-and-azure-ad"></a>Таблица 6а. Порты и протоколы для агента Azure AD Connect Health для (AD FS и синхронизации) и Azure AD
В этой таблице описываются следующие исходящие порты и протоколы, необходимые для взаимодействия между агентами Azure AD Connect Health и Azure AD.  

| Протокол | порты; | Описание |
| --- | --- | --- |
| HTTPS |443 (TCP или UDP) |Исходящие |
| Azure Service Bus |5671 (TCP или UDP) |Исходящие |

### <a name="6b---endpoints-for-azure-ad-connect-health-agent-for-(ad-fs/sync)-and-azure-ad"></a>6б. Конечные точки для агента Azure AD Connect Health для (AD FS и синхронизации) и Azure AD
Список конечных точек см. в [разделе "Требования" для агента Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md#requirements).

<!--HONumber=Oct16_HO2-->



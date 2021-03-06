---
title: Реализация синхронизации паролей с помощью службы Azure AD Connect Sync | Microsoft Docs
description: В этой статье объясняется, как работает синхронизация паролей и как ее включить.
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: markusvi;andkjell

---
# Реализация синхронизации паролей с помощью службы Azure AD Connect Sync
В этой статье содержатся сведения о том, как синхронизировать пароли пользователей локальной службы Active Directory (AD) и облачной службы Azure Active Directory (Azure AD).

## Что такое синхронизация паролей
Вероятность того, что сделанная работа может быть заблокирована из-за забытого пароля, связана с количеством разных паролей, которые необходимо помнить. Чем больше паролей необходимо помнить, тем выше вероятность забыть один из них. Вопросы и звонки о восстановлении паролей и прочие проблемы, связанные с паролями, забирают больше всего ресурсов службы технической поддержки.

Синхронизация паролей — это функция для синхронизации паролей пользователей локальной службы Active Directory и облачной службы Azure Active Directory (Azure AD). Эта функция позволяет входить в службы Azure Active Directory (например, Office 365, Microsoft Intune, CRM Online и доменные службы Azure AD) с помощью пароля, используемого для входа в локальную службу Active Directory.

![Что такое Azure AD Connect?](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

За счет сокращения числа паролей, которые должны помнить пользователи, до одного единственного, синхронизация паролей дает следующие преимущества:

* Повышение производительности пользователей
* Снижение затрат, связанных со службой технической поддержки

Если выбран параметр [**Федерация с AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), можно также включить синхронизацию паролей на случай сбоя в инфраструктуре AD FS.

Синхронизация паролей — это расширение функции синхронизации каталогов, реализованное при помощи служб синхронизации Azure AD Connect. Для использования синхронизации паролей в определенной среде необходимо выполнение следующих действий.

* Установка Azure AD Connect
* Настройка синхронизации служб каталогов между локальной службой AD и Azure Active Directory
* Включение синхронизации паролей

Дополнительные сведения см. в разделе [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

> [!NOTE]
> Дополнительную информацию о доменных службах Active Directory, настроенных для использования FIPS и синхронизации паролей, см. в разделе [Синхронизация паролей и FIPS](#password-synchronization-and-fips).
> 
> 

## Как работает синхронизация паролей
Доменная служба Active Directory хранит пароли в виде хэш-значений, которые представляют фактические пароли пользователей. Хэш-значение — это результат односторонней математической функции (*алгоритма хэширования*). Не существует метода для возврата результата односторонней функции в обычную текстовую версию пароля. Хэш пароля не может использоваться для входа в локальную сеть.

Чтобы синхронизировать пароль, Azure AD Connect Sync извлекает хэш пароля из локального каталога Active Directory. Перед синхронизацией в службу проверки подлинности Azure Active Directory хэш пароля дополнительно обрабатывается системой безопасности. Пароли синхронизируются для каждого пользователя отдельно и в хронологическом порядке.

Фактический поток данных в процессе синхронизации паролей аналогичен синхронизации данных пользователей, таких как отображаемые имена или адреса электронной почты. Но синхронизация паролей происходит чаще, чем это обычно делается для других атрибутов. Процесс синхронизации паролей выполняется каждые 2 минуты. Нельзя изменять частоту выполнения этого процесса. При синхронизации пароль перезаписывает существующий в облачной службе.

При первом включении функции синхронизации паролей она выполняет первоначальную синхронизацию паролей всех пользователей в области. Нельзя явно определить подмножество паролей пользователей, которые требуется синхронизировать.

При изменении локального пароля новый пароль, как правило, синхронизируется в течение нескольких минут. Если синхронизация паролей завершается сбоем, автоматически осуществляется новая попытка. Ошибки, возникающие во время синхронизации паролей, записываются в журнал событий.

Синхронизация пароля не влияет на пользователей, находящихся в системе в момент ее выполнения. Если изменение пароля синхронизируется во время вашего пребывания в облачной службе, оно не применяется в сеансе облачной службы немедленно. Однако, когда снова возникнет необходимость в проверке подлинности в облачной службе, потребуется указать новый пароль.

> [!NOTE]
> Синхронизация паролей поддерживается только для типа объектов user в Active Directory. Она не поддерживается для типа объектов iNetOrgPerson.
> 
> 

### Как синхронизация паролей работает с доменными службами Azure AD
Функцию синхронизации паролей также можно использовать для синхронизации локальных паролей с [доменными службами Azure AD](../active-directory-domain-services/active-directory-ds-overview.md). Данный сценарий позволяет доменным службам Azure AD проверять подлинность пользователей в облаке, применяя все методы, доступные в локальной службе AD. Действия в этом сценарии аналогичны использованию инструмента миграции Active Directory Migration Tool (ADMT) в локальной среде.

### Вопросы безопасности
При синхронизации пароли в текстовом формате не передаются в службу синхронизации паролей, Azure AD или какие-либо другие связанные службы.

Кроме того, в локальном каталоге Active Directory пароли могут храниться в зашифрованном виде и не подлежать восстановлению. При передаче данных между локальным каталогом AD и Azure Active Directory используется хэш-код пароля Active Directory. Хэш-код пароля не может использоваться для доступа к ресурсам вашей локальной среды.

### Рекомендации по политикам паролей
Есть два типа политик паролей, которые затрагивает синхронизация паролей:

1. Политика сложности паролей.
2. Политика срока действия паролей.

**Политика сложности паролей** При включении синхронизации паролей политики сложности паролей, настроенные в локальном каталоге Active Directory, переопределяют политики сложности, настроенные в облаке для синхронизированных пользователей. Все допустимые пароли из локального Active Directory можно использовать для доступа к службам Azure AD.

> [!NOTE]
> Пароли пользователей, созданные непосредственно в облаке, и впредь регулируются политиками паролей, которые настроены в облаке.
> 
> 

**Политика срока действия пароля** Если пароль пользователя синхронизируется, для пароля облачной учетной записи устанавливается *неограниченный срок действия*. Синхронизированный пароль, срок действия которого в локальной среде истек, можно использовать для входа в облачные службы. Пароль для облачной учетной записи изменяется, когда вы изменяете пароль в локальной среде.

### Перезапись синхронизированных паролей
Администратор может вручную сбросить пароль с помощью Windows PowerShell.

В таком случае новый пароль перезапишет синхронизированный пароль, и к новому паролю будут применены все политики паролей, настроенные в облаке.

Если вы снова измените локальный пароль, новый пароль синхронизируется в облако и перезапишет пароль, измененный вручную.

## Включение синхронизации паролей
Если во время установки Azure AD Connect используются **стандартные параметры**, синхронизация паролей включается по умолчанию. Дополнительные сведения см. в статье [Приступая к работе с Azure AD Connect с использованием стандартных параметров](active-directory-aadconnect-get-started-express.md).

Если во время установки Azure AD Connect используются пользовательские параметры, вам нужно включить синхронизацию паролей на странице входа в систему. Дополнительные сведения см. в статье [Выборочная установка Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Включение синхронизации паролей](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### Синхронизация паролей и FIPS
Если сервер заблокирован в соответствии с федеральным стандартом обработки информации (FIPS), схема MD5 отключена.

**Чтобы включить MD5 для синхронизации паролей выполните следующие действия.**

1. Перейдите к папке **%programfiles%\\Azure AD Sync\\Bin**.
2. Откройте файл **miiserver.exe.config**.
3. Перейдите на узел **конфигурации или среды выполнения** (в конце файла конфигурации).
4. Добавьте следующий узел: `<enforceFIPSPolicy enabled="false"/>`.
5. Сохраните изменения.

Этот фрагмент должен выглядеть следующим образом.

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Дополнительные сведения о безопасности и FIPS см. в записи блога [Синхронизация паролей, шифрование и соответствие FIPS в AAD](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## Устранение неполадок синхронизации паролей
Неполадки синхронизации паролей могут возникать либо в подмножестве пользователей, либо у всех.

* Если проблемы возникли с отдельными объектами, см. раздел [Устранение неполадок синхронизации паролей, связанных с одним объектом](#troubleshoot-one-object-that-is-not-synchronizing-passwords).
* Если пароли не синхронизируются, см. раздел [Устранение неполадок, связанных с синхронизацией паролей](#troubleshoot-issues-where-no-passwords-are-synchronized).

### Устранение неполадок синхронизации паролей, связанных с одним объектом
Проблемы, связанные с синхронизацией паролей, можно легко устранить, просмотрев состояние объекта.

Запустите оснастку **Пользователи и компьютеры Active Directory**. Найдите пользователя и снимите флажок **Потребовать смены пароля пользователя при следующем входе в систему**. ![Повышение производительности в Active Directory с помощью синхронизации паролей](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png) Если флажок установлен, пользователь должен войти в систему и сменить пароль. Временные пароли не синхронизируются с Azure AD.

Если в Active Directory этот флажок снят, далее необходимо отследить пользователя в модуле синхронизации. Отследив пользователя от локального Active Directory до Azure AD, вы сможете убедиться в отсутствии ошибок в объекте.

1. Запустите **[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)**.
2. Щелкните **Соединители**.
3. Выберите **соединитель Active Directory**, к которому относится пользователь.
4. Выберите **Search Connector Space** (Поиск пространства соединителя).
5. Найдите нужного пользователя.
6. Откройте вкладку **Журнал обращений и преобразований** и убедитесь, что по крайней мере для одного правила синхронизации параметр **Синхронизация паролей** имеет значение **True**. В конфигурации по умолчанию правило синхронизации имеет имя **In from AD - User AccountEnabled**. ![Сведения о журнале обращений и преобразований пользователя](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)
7. Затем [отследите пользователя](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) от метавселенной до пространства соединителя Azure AD. Объект пространства соединителя должен содержать правило исходящих подключений, в котором параметр **Синхронизация паролей** имеет значение **True**. В конфигурации по умолчанию это будет правило синхронизации с именем **Out to AAD - User Join**. ![Свойства пространства соединителя пользователя](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)
8. Чтобы просмотреть сведения о синхронизации паролей объекта за прошедшую неделю, щелкните **Журнал**. ![Сведения журнала объекта](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)

В столбце "Состояние" могут содержаться перечисленные ниже значения.

| Состояние | Description (Описание) |
| --- | --- |
| Успешно |Пароль успешно синхронизирован |
| FilteredByTarget |Для пароля установлено значение **Пользователь должен изменить пароль при следующем входе**. Пароль не синхронизирован. |
| NoTargetConnection |Объект отсутствует в метавселенной или в пространстве соединителя Azure AD. |
| SourceConnectorNotPresent |Объект не найден в локальном пространстве соединителя Active Directory. |
| TargetNotExportedToDirectory |Объект в пространстве соединителя Azure AD еще не экспортирован. |
| MigratedCheckDetailsForMoreInfo |Запись журнала создана до выхода версии 1.0.9125.0 и отображается в исходном состоянии. |

### Устранение неполадок, связанных с синхронизацией паролей
Начните с выполнения скрипта, описанного в разделе [Получение состояния параметров синхронизации паролей](#get-the-status-of-password-sync-settings). В этом разделе рассматривается конфигурация синхронизации паролей. ![Выходные данные сценария PowerShell из параметров синхронизации паролей](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png) Если в Azure AD не включена функция синхронизации паролей или не добавлено состояние канала синхронизации, запустите мастер установки Azure AD Connect. Выберите **Настроить параметры синхронизации** и снимите флажок "Синхронизация паролей". Это позволит временно отключить функцию. Затем снова запустите мастер установки и включите синхронизацию паролей. Запустите скрипт снова и проверьте правильность конфигурации.

Если конфигурация недопустима, тогда запустите скрипт, описанный в разделе [Запуск полной синхронизации всех паролей](#trigger-a-full-sync-of-all-passwords). Кроме того, этот скрипт можно использовать, если конфигурация правильная, но пароли не синхронизируются.

#### Получение состояния параметров синхронизации паролей
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### Запуск полной синхронизации всех паролей
Полную синхронизацию всех паролей можно активировать с помощью следующего скрипта:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## Дальнейшие действия
* [Azure AD Connect Sync: настройка параметров синхронизации](active-directory-aadconnectsync-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0824_2016-->
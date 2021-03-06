---
title: Установка Symantec Endpoint Protection на виртуальную машину | Microsoft Docs
description: Узнайте, как установить и настроить модуль безопасности Symantec Endpoint Protection на новой или существующей виртуальной машине Azure, созданной с помощью классической модели развертывания.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: iainfou

---
# Установка и настройка Symantec Endpoint Protection на виртуальной машине Windows
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

В этой статье показывается, как можно установить и настроить клиент Symantec Endpoint Protection на существующей виртуальной машине под управлением Windows Server. Это полный клиент, включающий такие службы, как защита от вирусов и шпионских программ, брандмауэр и система предотвращения вторжений. Клиент устанавливается как модуль безопасности с помощью агента ВМ.

При наличии действительной подписки от Symantec для локального решения можно использовать ее для защиты виртуальных машин Azure. Если у вас еще нет подписки, можно зарегистрироваться для получения пробной подписки. Дополнительные сведения об этом решении см. в статье [Symantec Endpoint Protection на платформе Microsoft Azure][Symantec]. На этой странице также приведены ссылки на сведения о лицензировании и инструкции по установке клиента для тех, кто уже использует Symantec.

## Установка Symantec Endpoint Protection на существующей виртуальной машине
Для этого потребуются следующие компоненты.

* Модуль Azure PowerShell версии не ниже 0.8.2, установленный на рабочем компьютере. Установленную версию Azure PowerShell можно проверить с помощью команды **Get-Module azure | format-table version**. Инструкции и ссылку на последнюю версию см. в статье [Установка и настройка Azure PowerShell][PS]. Войдите в свою подписку Azure с помощью `Add-AzureAccount`.
* Агент VM запущен на виртуальной машине Azure.

Сначала убедитесь, что агент VM уже установлен на виртуальной машине. Укажите имя облачной службы и имя виртуальной машины, а затем выполните следующие команды в командной строке Azure PowerShell уровня администратора. Замените все содержимое внутри кавычек, включая знаки < и >.

> [!TIP]
> Если вы не знаете имена облачной службы и виртуальной машины, выполните командлет **Get-AzureVM** и просмотрите список имен всех виртуальных машин в вашей текущей подписке.
> 
> 

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Если команда **write-host** отображает значение **True**, агент ВМ установлен. Если она отображает значение **False**, см. инструкции и ссылку для скачивания во [второй части записи блога Azure, посвященной агенту и расширениям виртуальных машин][Agent].

Если агент ВМ установлен, выполните следующие команды, чтобы установить агент Symantec Endpoint Protection.

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Чтобы проверить, установлен ли модуль безопасности Symantec, и убедиться в актуальности его версии, сделайте следующее:

1. Войдите в виртуальную машину. Указания см. в разделе [Как войти в виртуальную машину под управлением Windows Server][Logon].
2. Для Windows Server 2008 R2: щелкните **Пуск > Symantec Endpoint Protection**. Для Windows Server 2012 или Windows Server 2012 R2: на начальном экране введите **Symantec**, а затем щелкните **Symantec Endpoint Protection**.
3. На вкладке **Состояние** окна **Состояние Symantec Endpoint Protection** примените обновления или выполните перезагрузку, если это необходимо.

## Дополнительные ресурсы
[Как войти в виртуальную машину под управлением Windows Server][Logon]

[Расширения и компоненты виртуальных машин Azure][Ext]

<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493

<!---HONumber=AcomDC_0831_2016-->
---
title: Подготовка виртуального жесткого диска Windows к передаче в Azure | Microsoft Docs
description: Рекомендации по подготовке виртуального жесткого диска Windows к передаче в Azure.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2016
ms.author: glimoli;genli

---
# <a name="prepare-a-windows-vhd-to-upload-to-azure"></a>Подготовка виртуального жесткого диска Windows к передаче в Azure
Для передачи виртуальной машины Windows из локальной среды в Azure необходимо правильно подготовить виртуальный жесткий диск (VHD). Рекомендуется выполнить несколько шагов перед передачей VHD в Azure. В этой статье показано, как подготовить виртуальный жесткий диск Windows к передаче в Microsoft Azure. В ней также поясняется, [где и как использовать Sysprep](#step23).

## <a name="prepare-the-virtual-disk"></a>Подготовка виртуального диска
> [!NOTE]
> В Azure поддерживаются только [виртуальные машины первого поколения](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) , использующие формат файла VHD. Более новый формат VHDX не поддерживается в Azure. 
> 
> Размер VHD должен быть фиксированным, а не динамическим. На случай, если возникнет необходимость преобразовать VHDX-файлы или динамические диски, ниже приведены соответствующие инструкции. Максимально допустимый размер виртуального жесткого диска составляет 1023 ГБ.
> 
> 

Убедитесь в правильной работе виртуального жесткого диска Windows на локальном сервере. Устраните все ошибки в виртуальной машине, прежде чем ее преобразовывать или передавать в Azure.

Если необходимо преобразовать виртуальный диск в требуемый формат для Azure, используйте один из методов, указанных в следующих разделах. Перед запуском процесса преобразования виртуального диска или программы Sysprep выполните архивацию виртуальной машины.

### <a name="convert-using-hyper-v-manager"></a>Преобразование с помощью диспетчера Hyper-V
* Откройте диспетчер Hyper-V и выберите свой локальный компьютер в левой части окна. В меню над ним выберите **Действие** > **Изменить диск**.
  
  * На экране **Поиск виртуального жесткого диска** найдите и выберите свой виртуальный диск.
  * Выберите **Преобразовать** на следующем экране.
    
    * Если необходимо преобразовать диск в формате VHDX, выберите **VHD** и нажмите кнопку **Далее**.
    * Если необходимо преобразовать динамический диск, выберите **Фиксированный размер** и нажмите кнопку **Далее**.
  * Укажите **путь к новому VHD-файлу**.
  * Нажмите кнопку **Готово** , чтобы закрыть окно.

### <a name="convert-using-powershell"></a>Преобразование с помощью PowerShell
Можно преобразовать виртуальный диск с помощью командлета PowerShell [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). В следующем примере мы преобразовываем VHDX в VHD, а также преобразовываем динамический диск в диск с фиксированным размером.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### <a name="convert-from-vmware-vmdk-disk-format"></a>Преобразование диска VMware в формате VMDK
Если у вас есть образ виртуальной машины Windows [в формате VMDK](https://en.wikipedia.org/wiki/VMDK), преобразуйте его в VHD с помощью [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Дополнительные сведения см. в публикации [Преобразование VMDK VMWare в VHD Hyper-](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="prepare-windows-configuration-for-upload"></a>Подготовка конфигурации Windows к передаче
> [!NOTE]
> Выполните все приведенные ниже команды с [привилегиями администратора](https://technet.microsoft.com/library/cc947813.aspx).
> 
> 

1. Удалите все постоянные статические маршруты из таблицы маршрутизации.
   
   * Чтобы просмотреть таблицу маршрутизации, выполните команду `route print`.
   * Проверьте разделы **Persistence Routes** (Сохраняемые маршруты). При наличии постоянного маршрута удалите его с помощью команды [route delete](https://technet.microsoft.com/library/cc739598.apx) .
2. Удалите прокси-сервер WinHTTP.
   
    ```
    netsh winhttp reset proxy
    ```
3. Настройте для политики SAN дисков значение [Onlineall](https://technet.microsoft.com/library/gg252636.aspx).
   
    ```
    diskpart san policy=onlineall
    ```
4. Используйте время в формате UTC для Windows и установите для типа запуска службы времени Windows (w32time) значение **Автоматически**.
   
    ```
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    sc config w32time start= auto
    ```

## <a name="configure-windows-services"></a>Настройка служб Windows
1. Убедитесь, что для каждой из приведенных ниже служб Windows заданы **значения Windows по умолчанию**. Они настраиваются с помощью параметров запуска, которые указаны в следующем списке. Чтобы сбросить параметры загрузки, можно использовать приведенные ниже команды.
   
    ```
    sc config bfe start= auto
   
    sc config dcomlaunch start= auto
   
    sc config dhcp start= auto
   
    sc config dnscache start= auto
   
    sc config IKEEXT start= auto
   
    sc config iphlpsvc start= auto
   
    sc config PolicyAgent start= demand
   
    sc config LSM start= auto
   
    sc config netlogon start= demand
   
    sc config netman start= demand
   
    sc config NcaSvc start= demand
   
    sc config netprofm start= demand
   
    sc config NlaSvc start= auto
   
    sc config nsi start= auto
   
    sc config RpcSs start= auto
   
    sc config RpcEptMapper start= auto
   
    sc config termService start= demand
   
    sc config MpsSvc start= auto
   
    sc config WinHttpAutoProxySvc start= demand
   
    sc config LanmanWorkstation start= auto
   
    sc config RemoteRegistry start= auto
    ```

## <a name="configure-remote-desktop-configuration"></a>Настройка удаленного рабочего стола
1. Если к прослушивателю протокола удаленного рабочего стола (RDP) привязаны какие-либо самозаверяющие сертификаты, удалите их.
   
    ```
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```
   
    Дополнительные сведения о настройке сертификатов для прослушивателя RDP см. в разделе [Настройка сертификатов прослушивателя в Windows Server](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/).
2. Настройте значения [KeepAlive](https://technet.microsoft.com/library/cc957549.aspx) для службы RDP.
   
    ```
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f
   
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f
   
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```
3. Настройте режим аутентификации для службы RDP.
   
    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f
   
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f
   
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```
4. Включите службу RDP, добавив следующие подразделы в реестр.
   
    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```

## <a name="configure-windows-firewall-rules"></a>Настройка правил брандмауэра Windows
1. Разрешите трафик WinRM в трех профилях брандмауэра ("Домен", "Частный" и "Общий") и включите удаленную службу PowerShell.
   
   ```
   Enable-PSRemoting -force
   ```
2. Убедитесь, что заданы следующие правила брандмауэра для гостевой операционной системы.
   
   * Входящий трафик
   
   ```
   netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   
   netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
   
   * Исходящий и входящий
   
   ```
   netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   
   netsh advfirewall firewall set rule group="Core Networking" new enable=yes
   ```
   
   * Исходящие
   
   ```
   netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes
   
   netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
   ```

## <a name="additional-windows-configuration-steps"></a>Дополнительные шаги по настройке Windows
1. Выполните команду `winmgmt /verifyrepository` , чтобы подтвердить согласованность репозитория инструментария управления Windows (WMI). В случае повреждения репозитории ознакомьтесь с [этой записью блога](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).
2. Убедитесь, что заданы параметры данных конфигурации загрузки (BCD), как показано ниже.
   
   ```
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Удалите все дополнительные фильтры TDI, например программное обеспечение, которое анализирует пакеты TCP.
4. Чтобы убедиться, что диск работоспособен и согласован, выполните команду `CHKDSK /f` .
5. Удалите все стороннее программное обеспечение и драйверы, связанные с физическими компонентами или какой-либо другой технологией виртуализации.
6. Убедитесь в том, что никакое стороннее приложение не использует порт 3389. Этот порт используется для службы RDP в Azure. Можно использовать команду `netstat -anob` , чтобы проверить порты, которые используются приложениями.
7. Если требуется передать виртуальный жесткий диск Windows, который является контроллером домена, выполните [эти дополнительные шаги](https://support.microsoft.com/kb/2904015) по подготовке диска.
8. Перезагрузите виртуальную машину, чтобы убедиться в том, что ОС Windows по-прежнему работоспособна и доступна с помощью подключения к удаленному рабочему столу.
9. Сбросьте текущий пароль локального администратора и убедитесь, что эту учетную запись можно использовать для входа в Windows через подключение к удаленному рабочему столу.  Это разрешение на доступ контролируется объектом политики "Разрешить вход в систему через службу удаленных рабочих столов". Этот объект находится в папке Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment.

## <a name="install-windows-updates"></a>Установка обновлений Windows
1. Установите последние обновления для Windows. Если это невозможно, убедитесь, что установлены следующие обновления:
   
   * [KB3137061](https://support.microsoft.com/kb/3137061) : виртуальные машины Microsoft Azure не восстанавливаются после сбоя сети и возникают проблемы из-за повреждения данных.
   * [KB3115224](https://support.microsoft.com/kb/3115224) : улучшения надежности виртуальных машин, работающих на узле Windows Server 2012 R2 или Windows Server 2012.
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: обновление безопасности для Microsoft Windows, устраняющее проблему с повышением привилегий (8 марта 2016 г.).
   * [KB3063075](https://support.microsoft.com/kb/3063075) : при запуске виртуальной машины Windows Server 2012 R2 в Microsoft Azure регистрируется множество событий с идентификатором 129.
   * [KB3137061](https://support.microsoft.com/kb/3137061) : виртуальные машины Microsoft Azure не восстанавливаются после сбоя сети и возникают проблемы из-за повреждения данных.
   * [KB3114025](https://support.microsoft.com/kb/3114025) : низкая производительность при доступе к хранилищу файлов Azure из Windows 8.1 или Server 2012 R2.
   * [KB3033930](https://support.microsoft.com/kb/3033930) : исправление увеличивает предельное количество буферов RIO на процесс (64 000) для службы Azure в Windows.
   * [KB3004545](https://support.microsoft.com/kb/3004545) : невозможно получить доступ к виртуальным машинам, размещенным в службах размещения Azure, через VPN-подключение в Windows.
   * [KB3082343](https://support.microsoft.com/kb/3082343) : распределенные VPN-подключения утрачиваются, когда туннели VPN типа "сеть — сеть" Azure используют RRAS в Windows Server 2012 R2.
   * [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: обновление безопасности для Microsoft Windows, устраняющее проблему с повышением привилегий (8 марта 2016 г.).
   * [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: описание обновления для системы безопасности CSRSS от 12 апреля 2016 г.
   * [KB2904100](https://support.microsoft.com/kb/2904100): система перестает отвечать на запросы при дисковых операциях ввода-вывода в Windows <a id="step23"></a>
2. Если вы хотите создать образ для развертывания нескольких машин на его основе, то необходимо подготовить этот образ к использованию, выполнив команду `sysprep`, прежде чем передать VHD в Azure. Не нужно выполнять `sysprep` для использования специализированного виртуального жесткого диска. Дополнительные сведения о создании подготовленного к использованию образа см. в следующих статьях:
   
   * [Запись образа виртуальной машины Windows в модели развертывания диспетчера ресурсов](virtual-machines-windows-capture-image.md)
   * [Запись образа виртуальной машины Azure Windows, созданной с использованием классической модели развертывания](virtual-machines-windows-classic-capture-image.md)
   * [Sysprep Support for Server Roles (Поддержка ролей сервера в Sysprep)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

## <a name="suggested-extra-configurations"></a>Рекомендуемые дополнительные конфигурации
Приведенные ниже параметры не влияют на передачу VHD. Тем не менее настоятельно рекомендуется их настроить.

* Установите [агент виртуальной машины Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). После установки агента можно включить расширения виртуальной машины. Расширения виртуальной машины реализуют большую часть важных функций, которые могут вам понадобиться при работе с виртуальными машинами, в том числе сброс паролей, настройку протокола RDP и множество других функций.
* Журнал дампа может быть полезен при устранении критических неполадок Windows. Включите сбор журнала дампа, как показано ниже.
  
    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f
  
    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f
  
    sc config wer start= auto
    ```
* После создания виртуальной машины в Azure настройте определенный системой размер файла подкачки на диске D.
  
    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```

## <a name="next-steps"></a>Дальнейшие действия
* [Отправка образа виртуальной машины Windows в Azure для развертываний Resource Manager](virtual-machines-windows-upload-image.md)

<!--HONumber=Oct16_HO2-->



---
title: Подключение диска данных к виртуальной машине Windows | Microsoft Docs
description: Подключение нового или существующего диска данных к виртуальной машине Windows на портале Azure с помощью модели развертывания на основе диспетчера ресурсов.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# <a name="how-to-attach-a-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Подключение диска данных к виртуальной машине Windows на портале Azure
В этой статье демонстрируется подключение нового и существующего дисков к виртуальной машине Windows на портале Azure. Вы также можете [подключить диск данных к виртуальной машине Linux на портале Azure](virtual-machines-linux-attach-disk-portal.md). Перед этим ознакомьтесь со следующими советами:

* Размер виртуальной машины определяет, сколько дисков данных к ней можно подключить. Дополнительную информацию см. в статье [Размеры виртуальных машин](virtual-machines-windows-sizes.md).
* Для использования хранилища Premium вам необходимо будет использовать виртуальную машину серии DS или GS. Эти виртуальные машины позволяют использовать диски из учетных записей хранения Premium и Standard. Хранилище Premium доступно в определенных регионах. Дополнительные сведения см. в разделе [Хранилище класса Premium: высокопроизводительная служба хранилища для рабочих нагрузок виртуальных машин Azure](../storage/storage-premium-storage.md).
* На самом деле диски, подключенные к виртуальным машинам, — это VHD-файлы в учетной записи хранения Azure. Дополнительную информацию см. в статье [О дисках и виртуальных жестких дисках для виртуальных машин](virtual-machines-windows-about-disks-vhds.md).
* Если вы подключаете новый диск, его не надо предварительно создавать. Azure создаст его при подключении.
* Если вы подключаете существующий диск, его VHD-файл должен быть доступен в учетной записи хранения Azure. Можно использовать VHD, уже находящийся там, если он не подключен к другой виртуальной машине, или передать свой VHD-файл в учетную запись хранения.

[!INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="<a-id="initializeinws"></a>how-to:-initialize-a-new-data-disk-in-windows-server"></a><a id="initializeinWS"></a>Практическое руководство. Инициализация нового диска данных в Windows Server
1. Подключитесь к виртуальной машине. Указания см. в статье [Как подключиться к виртуальной машине Azure под управлением Windows и войти в систему](virtual-machines-windows-connect-logon.md).
2. После входа в систему на виртуальной машине откройте **Диспетчер сервера**. В области слева выберите **Файловые службы и службы хранилища**.
   
    ![Откройте диспетчер сервера.](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)
3. Разверните меню и выберите **Диски**.
4. В разделе **Диски** перечислены все диски. В большинстве случаев присутствует диск 0, диск 1 и диск 2. Диск 0 — это диск операционной системы, диск 1 — временный диск, а диск 2 — диск данных, который вы только что подключили к виртуальной машине. Новый диск данных будет отображаться в качестве раздела **Неизвестный**. Щелкните диск правой кнопкой мыши и выберите пункт **Инициализировать**.
5. Появится уведомление о том, что при инициализации диска все данные будут очищены. Щелкните **Да** , чтобы подтвердить ознакомление с предупреждением и инициализировать диск. После завершения операции раздел будет иметь значение **GPT**. Щелкните диск правой кнопкой мыши еще раз и выберите пункт **Новый том**.
6. Завершите мастер, используя значения по умолчанию. После этого в разделе **Тома** появится новый том. Теперь диск в сети и готов к хранению данных.

    ![Том успешно инициализирован](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [!NOTE]
> Размер виртуальной машины определяет количество дисков, которые можно к ней подключить. Дополнительную информацию см. в статье [Размеры виртуальных машин](virtual-machines-linux-sizes.md).
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Если вашему приложению нужно использовать диск D для хранения данных, вы можете [изменить букву временного диска Windows](virtual-machines-windows-classic-change-drive-letter.md).

<!--HONumber=Oct16_HO2-->



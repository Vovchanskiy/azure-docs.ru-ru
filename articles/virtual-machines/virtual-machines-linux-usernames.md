---
title: Выбор имен пользователей для Linux | Microsoft Docs
description: Узнайте, как выбирать имена пользователей для виртуальной машины Linux в Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
ms.author: szark

---
# Выбор имен пользователей для Linux в Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

При подготовке виртуальной машины Linux в Azure необходимо указать имя непривилегированного пользователя, которое позже можно будет использовать для входа в виртуальную машину. Можно выбрать имя нового пользователя или (при подготовке с помощью классического портала Azure) принять имя по умолчанию azureuser.

В большинстве случаев этот новый пользователь не существует в базовом образе и создается в процессе подготовки. Если пользователь существует в базовом образе виртуальной машины, агент Azure для Linux просто настраивает для него пароль (или ключ SSH) на основе данных, указанных при создании виртуальной машины.

**Тем не менее** Linux определяет набор имен пользователей, которые нельзя использовать. Процесс подготовки завершится **ошибкой**, если при подготовке виртуальной машины Linux вы попробуете использовать данные существующего системного пользователя, который определен как пользователь с идентификатором в диапазоне 0—99. Типичный пример — пользователь `root` с идентификатором 0.

* См. также: [База стандартов Linux: диапазоны идентификаторов пользователей](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

Ниже приведен список наиболее распространенных встроенных системных пользователей для CentOS и Ubuntu, которые не следует использовать при подготовке виртуальной машины Linux в Azure. Этот список является примером; чтобы убедиться, что выбранное имя пользователя не конфликтует с существующим системным пользователем, см. документацию по вашему дистрибутиву.

## CentOS
* abrt
* adm
* audio
* bin
* cdrom
* cgred
* daemon
* dbus
* dialout
* dip
* disk
* floppy
* ftp
* ftp
* games
* gopher
* haldaemon
* halt
* kmem
* lock
* lp
* mail
* man
* mem
* nfsnobody
* nobody
* ntp
* operator
* oprofile
* postdrop
* postfix
* qpidd
* root
* rpc
* rpcuser
* saslauth
* shutdown
* slocate
* sshd
* stapdev
* stapusr
* sync
* sys
* tape
* test
* tcpdump
* tty
* users
* utempter
* utmp
* uucp
* vcsa
* video
* wheel

## Ubuntu
* adm
* admin
* audio
* backup
* bin
* cdrom
* crontab
* daemon
* dialout
* dip
* disk
* fax
* floppy
* fuse
* games
* gnats
* irc
* kmem
* landscape
* libuuid
* list
* lp
* mail
* man
* messagebus
* mlocate
* netdev
* news
* nobody
* nogroup
* operator
* plugdev
* proxy
* root
* sasl
* shadow
* src
* ssh
* sshd
* staff
* sudo
* sync
* sys
* syslog
* tape
* tty
* users
* utmp
* uucp
* video
* voice
* whoopsie
* www-data

<!---HONumber=AcomDC_0713_2016-->
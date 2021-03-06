# Как создать настраиваемую виртуальную машину
*Настраиваемая* виртуальная машина — это виртуальная машина, созданная с помощью метода **Из коллекции**, что дает вам больший выбор параметров, чем метод **Быстрое создание**. Вот какие параметры доступны:

* Больший выбор образов для создания виртуальной машины.
* Подключение виртуальной машины к виртуальной сети.
* Добавление виртуальной машины к существующей облачной службе.
* Добавление виртуальной машины в группу доступности.

> [!IMPORTANT]
> Если к виртуальной машине необходимо подключаться с помощью имени узла по виртуальной сети или настроить подключения между организациями, при создании виртуальной машины обязательно укажите виртуальную сеть. Виртуальную машину можно настроить для подключения к виртуальной сети только при ее создании. Дополнительные сведения о виртуальных сетях см. в разделе [Обзор виртуальных сетей Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

1. Войдите на [портал Azure](http://manage.windowsazure.com).
2. На панели команд нажмите **Создать**.
3. Нажмите кнопку **Среда выполнения приложений**, нажмите **Виртуальная машина**, а затем **Из коллекции**.
4. Выберите образ, который хотите использовать, и нажмите кнопку со стрелкой, чтобы продолжить.
5. Если в окне **Дата выпуска версии** доступны образы нескольких версий, выберите необходимую версию.
6. В поле **Имя виртуальной машины** введите имя, которое вы хотите использовать для виртуальной машины.
7. С помощью полей **Уровень** и **Размер** выберите подходящий размер виртуальной машины. Выбранный вами размер определит максимальную конфигурацию виртуальной машины, а также цены. Дополнительную информацию о конфигурации см. в разделе [Размеры виртуальных машин и облачных служб для Azure](http://go.microsoft.com/fwlink/p/?LinkID=389844).
8. В поле **Новое имя пользователя** введите имя учетной записи администратора, которую требуется использовать для управления сервером.
9. В поле **Новый пароль** введите надежный пароль для учетной записи администратора. В поле **Подтвердите пароль** введите тот же пароль еще раз.
10. Щелкните стрелку для продолжения.
11. Во вкладке **Облачная служба** выполните одно из следующих действий:
    
    * Если это первая или единственная виртуальная машина в облачной службе, выберите **Создать новую облачную службу**. Затем в поле **DNS-имя облачной службы** введите имя из 3—24 букв нижнего регистра и цифр. Это имя становится частью URI, который используется для связи с виртуальной машиной в облачной службе.
    * Если эта виртуальная машина добавляется к облачной службе, выберите ее из списка.
    
    > [!NOTE]
    > Дополнительную информацию о размещении виртуальных машин в той же облачной службе см. в разделе [Подключение к виртуальным машинам в облачной службе](https://azure.microsoft.com/manage/windows/how-to-guides/connect-to-a-cloud-service/).
    > 
    > 
12. В поле **Регион/Территориальная группа/Виртуальная сеть** выберите регион, территориальную группу или виртуальную сеть, которую требуется использовать для виртуальной машины. Дополнительные сведения о территориальных группах см. в разделе [О территориальных группах для виртуальной сети](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md)
13. В поле **Учетная запись хранения** выберите существующую учетную запись хранения для VHD-файла или используйте автоматически созданную учетную запись хранения. Автоматически будет создана только одна учетная запись хранения для одного региона. Все остальные виртуальные машины, созданные с помощью этого параметра, находятся в этой учетной записи хранения. Вы можете иметь не более 20 учетных записей хранения.
14. Если требуется, чтобы виртуальная машина принадлежала к группе доступности, выберите во вкладке **Группа доступности** вариант **Создать группу доступности** или добавьте ее к существующей группе доступности.
    
    **Примечание**. Виртуальные машины в группе доступности развертываются в разные домены сбоя. Размещение нескольких виртуальных машин в группе доступности помогает обеспечить, что ваше приложение остается доступным во время сбоев сети или локальных жестких дисков, а также при плановых простоях.
15. В разделе **Конечные точки** просмотрите новые конечные точки, которые будут созданы для подключений к виртуальной машине, например через удаленный рабочий стол или клиент протокола безопасной оболочки (SSH). Вы также можете добавить конечные точки сейчас или создать их позже. Инструкции по их созданию см. в разделе [Настройка конечных точек с виртуальной машиной](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).
16. В разделе **Агент ВМ** укажите, следует ли установить агент ВМ. Этот агент предоставляет среду для установки расширений, которые помогут вам взаимодействовать с виртуальной машиной. Дополнительные сведения см. в разделе [Управление расширениями](http://go.microsoft.com/FWLink/p/?LinkID=390493).
17. Чтобы создать виртуальную машину, нажмите на кнопку со стрелкой.
    
    ![Настраиваемая виртуальная машина успешно создана](./media/howto-custom-create-vm/VMSuccessWindows.png)

## Дальнейшие действия
После создания виртуальной машины она запускается автоматически. Когда отображается состояние портала "Выполняется", можно войти в виртуальную машину. Указания см. в одной из следующих статей:

* [Как войти в виртуальную машину под управлением Linux](../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)
* [Как войти в виртуальную машину под управлением Windows Server](../articles/virtual-machines/virtual-machines-windows-classic-connect-logon.md)

<!---HONumber=AcomDC_0824_2016-->
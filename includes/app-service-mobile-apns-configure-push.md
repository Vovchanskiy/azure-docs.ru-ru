

1. На компьютере Mac запустите программу **Keychain Access**. Откройте раздел **Категория** и выберите **Сертификаты**. Найдите загруженный ранее SSL-сертификат, который нужно экспортировать, и откройте его. Выберите только сертификат (без закрытого ключа) и [экспортируйте его](https://support.apple.com/kb/PH20122?locale=en_US).
2. На портале Azure щелкните **Просмотреть все** > **Службы приложений** > ваш сервер мобильных приложений > **Параметры** > **Мобильные** > **Службы push-уведомлений** > **Настройка обязательных параметров** > **+ Центр уведомлений**, затем укажите для центра уведомлений имя и пространство имен и нажмите кнопку **ОК**.
   
      ![][1]
3. В колонке **Создать концентратор уведомлений** нажмите кнопку **Создать**.
   
    Прежде чем перейти к следующему шагу, щелкните пункт **Уведомления**, чтобы убедиться в том, что концентратор уведомлений полностью настроен.
4. В предварительной версии портала Azure щелкните **Просмотреть все** > **Службы приложений** > ваш сервер мобильных приложений > **Параметры** > **Мобильные** > **Push-уведомления** > **Apple Push Notification Services** > **Загрузить сертификат**. Передайте P12-файл, выбрав соответствующий **режим** (в зависимости от того, какой SSL-сертификат клиента был создан ранее — для разработки или распространения). Теперь ваша служба настроена для работы с push-уведомлениями в iOS!

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png

<!---HONumber=AcomDC_1203_2015-->
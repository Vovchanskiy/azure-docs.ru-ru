### Создание консольного приложения и получение сборки
Создать консольное приложение в Visual Studio и установить пакет NuGet, содержащий клиентскую библиотеку службы хранилища Azure, можно так.

1. В Visual Studio последовательно выберите элементы **Файл -> Создать проект**, а затем в списке шаблонов Visual C# выберите **Windows -> Консольное приложение**.
2. Укажите имя консольного приложения и нажмите кнопку **ОК**.
3. После создания проекта щелкните правой кнопкой мыши на проекте в обозревателе решений и выберите **Управление пакетами NuGet**. Выполните в Интернете поиск по запросу "WindowsAzure.Storage" и нажмите кнопку **Установить**, чтобы установить клиентскую библиотеку службы хранилища Azure для пакета .NET и зависимые компоненты.

В примере кода для этой статьи также используется [библиотека диспетчера конфигураций Microsoft Azure](https://msdn.microsoft.com/library/azure/mt634646.aspx) для получения строки подключения из файла app.config в консольном приложении. С помощью диспетчера конфигураций Azure можно получить строку подключения во время выполнения независимо от того, выполняется ли приложение в Microsoft Azure или как классическое, мобильное или веб-приложение.

Чтобы установить пакет менеджера конфигураций Azure, щелкните правой кнопкой мыши проект в обозревателе решений и выберите пункт **Управление пакетами NuGet**. Выполните поиск в Интернете по запросу "ConfigurationManager" и нажмите кнопку **Установить**, чтобы установить пакет.

Использование диспетчера конфигураций Azure не является обязательным. Вы также можете использовать API, например [класс ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) для .NET Framework.

<!---HONumber=AcomDC_0309_2016-->
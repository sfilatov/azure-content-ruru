<properties
	pageTitle="Подключение Operations Manager к Log Analytics | Microsoft Azure"
	description="Чтобы не увеличивать затраты на System Center Operations Manager и использовать расширенные возможности в Log Analytics, Operations Manager можно интегрировать с рабочей областью OMS."
	services="log-analytics"
	documentationCenter=""
	authors="MGoedtel"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2016"
	ms.author="magoedte"/>

# Подключение Operations Manager к Log Analytics

Чтобы не увеличивать затраты на System Center Operations Manager и использовать расширенные возможности в Log Analytics, Operations Manager можно интегрировать с рабочей областью OMS. Таким образом вы сможете использовать функции OMS, располагая следующими возможностями благодаря интеграции с Operations Manager:

- мониторинг работоспособности ИТ-служб с помощью Operations Manager;
- управление интеграцией с решениями ITSM, поддерживающими управление различными инцидентами и неполадками;
- управление жизненным циклом агентов, развернутых на виртуальных машинах IaaS в локальных и общедоступных облаках, мониторинг работоспособности в которых выполняется с помощью Operations Manager.

Благодаря интеграции с System Center Operations Manager вы получаете скорость и эффективность решения OMS для сбора, хранения и анализа данных из Operations Manager. OMS позволяет выполнять сопоставление проблем, определять ошибки и сообщать о повторном возникновении для поддержки существующего процесса управления проблемами. Гибкая поисковая система, позволяющая просматривать данные о производительности, событиях и оповещениях, а также расширенные возможности панелей мониторинга и создания отчетов свидетельствуют о широких возможностях, которые предоставляются после интеграции Operations Manager с OMS.

Агенты, которые создают отчеты для группы управления Operations Manager, собирают данные с серверов на основе источников данных и решений Log Analytics, которые вы включили в подписку OMS. В зависимости от включенных решений данные с этих решений отправляются непосредственно из сервера управления Operations Manager в веб-службу OMS или непосредственно из агента в веб-службу OMS, что зависит от объема данных, собранных в управляемой агентом системе. Сервер управления перенаправляет данные OMS непосредственно в веб-службу OMS. Они никогда не записываются в базу данных OperationsManager или OperationsManagerDW. Если подключение между сервером управления и веб-службой OMS разорвано, данные на сервере управления кэшируются локально, пока подключение не будет восстановлено. Если сервер управления находится в автономном режиме из-за планового обслуживания или сбоя, подключение к OMS будет установлено с другого сервера в группе управления.

На следующей схеме показана связь System Center Operations Manager и OMS.

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

## Требования к системе
Перед началом интеграции необходимо выполнить следующие требования.

- Обеспечение поддержки OMS. OMS поддерживает только Operations Manager 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 6 (UR6) и более поздней версии и Operations Manager 2012 R2 с накопительным пакетом обновления 2 (UR2) и более поздней версии. Поддержка прокси-сервера была добавлена в Operations Manager 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 7 (UR7) и в Operations Manager 2012 R2 с накопительным пакетом обновления 3 (UR3).
- Все агенты Operations Manager должны удовлетворять минимальным требованиям поддержки. Убедитесь, что агенты соответствуют минимальным требованиям, иначе при передаче трафика агента Windows возникнет сбой, а журнал событий Operations Manager заполнит множество ошибок.
- Наличие подписки OMS. Дополнительные сведения см. в статье [Начало работы с Log Analytics](log-analytics-get-started.md).

## Подключение Operations Manager к OMS
Выполните следующие действия, чтобы настроить группу управления Operations Manager для подключения к одной из рабочих областей OMS.

1. В консоли Operations Manager щелкните рабочую область **Администрирование**.
2. Разверните узел Operations Management Suite и щелкните **Подключение**.
3. Щелкните ссылку **Register to Operations Management Suite** (Зарегистрироваться в Operations Management Suite).
4. На странице **Operations Management Suite Onboarding Wizard: Authentication** (Мастер переноса Operations Management Suite: аутентификация) введите адрес электронной почты или номер телефона и пароль учетной записи администратора, связанной с подпиской OMS, и нажмите кнопку **Войти**.
5. После успешного прохождения аутентификации на странице **Operations Management Suite Onboarding Wizard: Select Workspace** (Мастер переноса Operations Management Suite: аутентификация) вам будет предложено выбрать рабочую область OMS. При наличии нескольких рабочих областей выберите в раскрывающемся списке рабочую область, которую необходимо зарегистрировать в группе управления Operations Manager, и нажмите кнопку **Далее**.

    >[AZURE.NOTE] Одновременно в Operations Manager поддерживается только одна рабочая область OMS. Подключения и компьютеры, зарегистрированные в OMS с предыдущей рабочей областью, удаляются из OMS.

6. На странице **Operations Manager Suite Onboarding Wizard: Summary** (Мастер переноса Operations Management Suite: сводка) подтвердите параметры и нажмите кнопку **Создать**.
7. На странице **Operations Management Suite Onboarding Wizard: Finish** (Мастер переноса Operations Management Suite: готово) нажмите кнопку **Закрыть**.

### Добавление управляемых агентом компьютеров
Настройка интеграции с рабочей областью OMS позволяет только установить подключение к OMS. При этом сбор данных из агентов, которые создают отчеты для группы управления, выполняться не будет. Для этого необходимо указать определенные управляемые агентом компьютеры, которые будут собирать данные для Log Analytics. Можно выбрать компьютеры по отдельности или выбрать группу, в которой содержатся компьютеры Windows. Нельзя выбрать группу, в которой содержатся экземпляры другого класса, например логические диски или базы данных SQL.

1. Откройте консоль Operations Manager и выберите рабочую область **Администрирование**.
2. Разверните узел Operations Management Suite и щелкните **Подключение**.
3. В разделе "Действия" с правой стороны панели щелкните ссылку **Добавить компьютер/группу**.
4. В диалоговом окне **Поиск компьютера** можно выполнить поиск компьютеров или групп, отслеживаемых с помощью Operations Manager. Выберите компьютеры или группы для подключения к OMS, щелкните **Добавить**, а затем нажмите кнопку **ОК**.

Компьютеры и группы, настроенные для сбора данных с узла управляемых компьютеров, можно просмотреть в разделе Operations Management Suite в рабочей области **Администрирование** консоли управления. Здесь также можно при необходимости добавлять или удалять компьютеры и группы.

### Настройка параметров прокси-сервера OMS в консоли управления
Если внутренний прокси-сервер расположен между группой управления и веб-службой OMS, выполните действия, указанные ниже. Этими параметрами можно централизовано управлять из группы управления, и они распределены между управляемыми агентами системами, которые используются при сборе данных для OMS. Это дает преимущества, если определенные решения обходят сервер управления и отправляют данные непосредственно в веб-службу OMS.

1. Откройте консоль Operations Manager и выберите рабочую область **Администрирование**.
2. Разверните узел Operations Management Suite и щелкните **Подключения**.
3. В представлении OMS Connection (Подключение к OMS) щелкните **Настройка прокси-сервера**.
4. На странице **Operations Management Suite Wizard: Proxy Server** (Мастер переноса Operations Management Suite: прокси-сервер) выберите **Use a proxy server to access the Operations Management Suite** (Использовать прокси-сервер для доступа к Operations Management Suite), а затем введите URL-адрес с номером порта (например, http://corpproxy:80) и нажмите кнопку **Готово**.

Если для доступа к прокси-серверу необходимо проходить аутентификацию, выполните указанные ниже действия, чтобы настроить учетные данные и параметры, которые необходимо распространить между управляемыми компьютерами, которые будут отправлять отчеты в OMS, в группе управления.

1. Откройте консоль Operations Manager и выберите рабочую область **Администрирование**.
2. В разделе **Конфигурации запуска от имени** выберите **Профили**.
3. Откройте профиль **Запуск от имени прокси-сервера профиля System Center Advisor**.
4. В мастере профилей запуска от имени нажмите кнопку "Добавить", чтобы использовать учетную запись запуска от имени. Можно создать новую [учетную запись запуска от имени](https://technet.microsoft.com/library/hh321655.aspx) или использовать существующую. Эта учетная запись должна иметь достаточные разрешения для передачи данных через прокси-сервер.
5. Чтобы настроить учетную запись для управления, выберите **Выбранный класс, группа или объект**, щелкните **Выбрать…**, а затем выберите **Group…** (Группа…), чтобы открыть поле поиска **Поиск групп**.
6. Найдите и выберите **группу серверов мониторинга Microsoft System Center Advisor**. Нажмите кнопку **ОК**, чтобы закрыть поле поиска **Поиск групп** после выбора группы.
7.	Нажмите кнопку **ОК**, чтобы закрыть поле **Добавление учетной записи запуска от имени**.
8.	Нажмите кнопку **Сохранить**, чтобы закрыть мастер и сохранить изменения.

После создания подключения и настройки агентов для сбора и отправки данных в OMS для группы управления будет выполнено следующее (не обязательно в таком же порядке):

- Будет создана учетная запись запуска от имени **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate**. Она связана с профилем запуска от имени **Microsoft System Center Advisor Run As Profile Blob** (Запуск от имени большого двоичного объекта профиля Microsoft System Center Advisor) и предназначена для двух классов: **сервера сбора** и **группы управления Operations Manager**.
- Будут созданы два соединителя. Первый — **Microsoft.SystemCenter.Advisor.DataConnector**. Он автоматически настраивается с подпиской, которая перенаправляет все оповещения, созданные из экземпляров всех классов, в группу управления в OMS Log Analytics. Второй — **соединитель Advisor**. Он отвечает за взаимодействие с веб-службой OMS и обмен данными.
- Агенты и группы, выбранные для сбора данных в группе управления, будут добавлены в **группу серверов мониторинга Microsoft System Center Advisor**.

## Обновления пакета управления
После завершения настройки группа управления Operations Manager устанавливает подключение к службе OMS. Сервер управления синхронизируется с веб-службой и получает обновленные сведения о конфигурации в виде пакетов управления для решений, которые вы включили для интеграции с Operations Manager. Operations Manager проверяет наличие обновлений для этих пакетов управления, автоматически скачивает и импортирует их. Существует два правила для управления этими действиями:

- **Microsoft.SystemCenter.Advisor.MPUpdate**: обновляет базовые пакеты управления OMS. По умолчанию выполняется каждые 12 (двенадцать) часов.
- **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule**: обновляет пакеты управления решения, включенные в рабочую область. По умолчанию выполняется каждые 5 (пять) минут.

Эти два правила можно переопределить, чтобы предотвратить автоматическое скачивание, путем их отключения или изменить частоту синхронизации сервера управления с OMS для определения доступности нового пакета управления и необходимости его скачивания. Чтобы изменить параметр **Частота** со значением в секундах для изменения расписания синхронизации или изменить параметр **Включено** для отключения правил, выполните действия, описанные в статье [Переопределение правила или монитора состояния](https://technet.microsoft.com/library/hh212869.aspx). Примените переопределенные значения для всех объектов класса группы управления Operations Manager.

Если изменять существующий процесс управления изменениями для контроля выпусков пакетов управления в рабочей группе управления не требуется, отключите правила и включайте их только тогда, когда требуются обновления. При наличии в среде группы управления разработкой и контролем качества и подключения к Интернету, для поддержки этого сценария эту группу управления можно настроить с рабочей областью OMS. Таким образом вы сможете просматривать и оценивать последовательные выпуски пакетов управления OMS перед их выпуском в вашей рабочей группе управления.

## Перевод группы Operations Manager в новую рабочую область OMS
1. Войдите в свою подписку OMS и создайте новую рабочую область в [Microsoft Operations Management Suite](http://oms.microsoft.com/).
2. Откройте консоль Operations Manager под учетной записью пользователя, который является членом роли администраторов Operations Manager, и выберите **администрирование** рабочей области.
3. Разверните узел Operations Management Suite и выберите **Подключения**.
4. Щелкните ссылку **Повторная настройка Operation Management Suite** в середине панели.
5. Выполните инструкции **мастера переноса Operations Management Suite** и введите адрес электронной почты или номер телефона и пароль учетной записи администратора, связанной с новой рабочей областью OMS.

    > [AZURE.NOTE] На странице **Operations Management Suite Onboarding Wizard: Select Workspace** (Мастер переноса Operations Management Suite: выбор рабочей области) будет указана используемая рабочая область.


## Проверка интеграции Operations Manager с OMS
Существует несколько разных способов для проверки интеграции OMS с Operations Manager.

### Подтверждение интеграции на портале OMS

1.	На портале OMS щелкните элемент **Параметры**.
2.  Выберите **Подключенные источники**.
3.	В разделе System Center Operations Manager вы увидите название группы управления с указанием числа агентов и состояния, а также сведениями о последнем получении данных.

    ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)

4.  Обратите внимание на значение **Идентификатор рабочей области** в левой части страницы "Параметры". Его нужно будет проверить по группе управления Operations Manager.

### Подтверждение интеграции в консоли управления

1.	Откройте консоль Operations Manager и выберите рабочую область **Администрирование**.
2.	Выберите **Пакеты управления** и в текстовом поле **Поиск** введите **Помощник** или **Аналитика**.
3.	В зависимости от решений, которые вы включили, в результатах поиска отобразится соответствующий пакет управления. Например, если вы включили решение для управления оповещениями, в списке отобразится пакет управления Microsoft System Center Advisor Alert Management.
4.  Из представления **Мониторинг** перейдите в представление **Operations Management Suite: состояние работоспособности**. Выберите сервер управления в области **Состояние сервера управления** области, а затем в области **Подробное представление** проверьте, соответствует ли значение свойства **универсальный код ресурса (URI) службы проверки подлинности** идентификатору рабочей области OMS.

    ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)


## Удаление интеграции с OMS
Если интеграция группы управления Operations Manager и рабочей области OMS больше не требуется, для надлежащего удаления подключения и конфигурации в группе управления необходимо выполнить несколько действий. Описанная ниже процедура позволит вам обновить рабочую область OMS, удалив ссылку на группу управления, удалить соединители OMS, а затем удалить пакеты управления, поддерживающие OMS.

1.  На портале OMS щелкните элемент **Параметры**.
2.	Выберите **Подключенные источники**.
3.	В таблице в разделе System Center Operations Manager должно быть указано имя группы управления, которую нужно удалить из рабочей области. В столбце **Последние данные** нажмите кнопку **Удалить**.  
4.	Откроется окно с запросом подтверждения удаления. Нажмите кнопку **Да**, чтобы продолжить.  
5.	Откройте командную оболочку Operations Manager под учетной записью пользователя, который является членом роли администраторов Operations Manager.

    >[AZURE.WARNING] Прежде чем продолжить, убедитесь, что у вас нет пользовательских пакетов управления, в названии которых есть слово Advisor или IntelligencePack, иначе при выполнении указанных ниже действий они будут удалены из группы управления.

6.	В командной оболочки введите `Get-SCOMManagementPack -name "*advisor*" | Remove-SCOMManagementPack`.

7.	Затем введите `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack`.

8.	Откройте консоль операций Operations Manager под учетной записью пользователя, который является членом роли администраторов Operations Manager.
9.	В разделе **Администрирование** выберите узел **Пакеты управления**, а в поле **Искать:** введите **Advisor** и убедитесь, что в группу управления по-прежнему импортируются следующие пакеты управления:

    - Microsoft System Center Advisor;
    - Microsoft System Center Advisor Internal.

Чтобы удалить два соединителя — Microsoft.SystemCenter.Advisor.DataConnector и Advisor, сохраните приведенный ниже скрипт PowerShell на свой компьютер и выполните его, как показано в следующих примерах.

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementGroupName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementGroupName>
```

>[AZURE.NOTE] Если компьютер, на котором выполняется этот скрипт, не является сервером управления, на нем должна быть установлена командная оболочка Operations Manager 2012 с пакетом обновления 1 (SP1) или R2 в зависимости от версии вашей группы управления.

```
    `param(
    [String] $connectorName,
    [String] $mgName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $mgName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

Если в будущем вы захотите снова подключить свою группу управления к рабочей области OMS, вам потребуется повторно импортировать файл пакета управления `Microsoft.SystemCenter.Advisor.Resources.<Language>\.mpb` из последней версии накопительного пакета обновления для вашей группы управления. Этот файл находится в папке `%ProgramFiles%\Microsoft System Center 2012` или `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## Дальнейшие действия

- Чтобы добавить функции и реализовать сбор данных, [добавьте решения Log Analytics из коллекции решений](log-analytics-add-solutions.md).
- Если ваша организация использует прокси-сервер или брандмауэр, [настройте прокси-сервер и брандмауэр в Log Analytics](log-analytics-proxy-firewall.md), чтобы агенты могли взаимодействовать со службой Log Analytics.

<!---HONumber=AcomDC_0518_2016-->
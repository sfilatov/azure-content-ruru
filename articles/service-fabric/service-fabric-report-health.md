<properties
   pageTitle="Добавление настраиваемых отчетов о работоспособности Service Fabric | Microsoft Azure"
   description="Здесь описано, как отправлять настраиваемые отчеты о работоспособности в сущности работоспособности Azure Service Fabric. Здесь собраны рекомендации по разработке и реализации качественных отчетов о работоспособности."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="oanapl"/>

# Добавление настраиваемых отчетов о работоспособности Service Fabric
В Azure Service Fabric представлена [модель работоспособности](service-fabric-health-introduction.md), которая предназначена для обозначения условий неработоспособности кластеров или приложений в отдельных сущностях. Этим занимаются **информаторы о работоспособности** (системные компоненты и устройства наблюдения). Их целью является простая и быстрая диагностика и восстановление. Создатели службы должны предупреждать проблемы работоспособности. Любые условия, которые могут повлиять на работоспособность, должны регистрироваться, особенно в случаях, если это может помочь выяснить причину возникновения проблем. Благодаря этому можно сэкономить время и не выполнять дополнительные отладки и исследования после полноценного запуска службы в облаке (частном или Azure).

Докладчики Service Fabric отслеживают определенные условия. Они сообщают об этих условиях на основе своего локального представления. В [хранилище данных о работоспособности](service-fabric-health-introduction.md#Health-Store) собираются данные о работоспособности, отправленные всеми информаторами, что помогает определять глобальную работоспособность сущностей. Модель оптимизирована, она гибкая и простая в использовании. Качество отчетов о работоспособности определяет точность представления данных о работоспособности кластера. Ложные положительные результаты, которые неправильно отображают проблемы работоспособности, могут отрицательно повлиять на обновления или другие службы, использующие данные о работоспособности. Это могут быть службы восстановления или механизмы предупреждений. Поэтому нужно тщательно обдумать, как получить отчеты с отображением только нужных условий.

Для разработки и реализации отчетности о работоспособности устройства наблюдения и компоненты системы должны выполнить указанные ниже действия.

- Задать нужные условия, способ их отслеживания и влияние на работу кластера или приложения. Этим вы определите свойства отчета о работоспособности и состояние работоспособности.

- Определить [сущность](service-fabric-health-introduction.md#health-entities-and-hierarchy), к которой применяется отчет.

- Указать, откуда должен выполняться отчет — из службы, внутреннего или внешнего устройства наблюдения.

- Укажите источник, используемый для идентификации докладчика.

- Выберите стратегию создания отчетов — периодическую или на основе переходов. Рекомендуем выбирать периодическую отчетность, так как она требует более простого кода и меньше подвержена ошибкам.

- Указать срок хранения отчетов о неработоспособности в хранилище данных о работоспособности и правила их очистки. Так определяется период существования отчета и его удаление по истечении срока хранения.

Как упоминалось выше, отчет могут создавать следующие компоненты.

- Отслеживаемая реплика службы Service Fabric.

- Внутренние устройства наблюдения, развернутые как служба Service Fabric (например, служба без отслеживания состояния Service Fabric, которая отслеживает условия и формирует отчеты). Модули наблюдения можно развернуть на всех узлах или сгруппировать с отслеживаемой службой

- Внутренние устройства наблюдения, которые запускаются на узлах Service Fabric, но *не* реализованы как службы Service Fabric.

- Внешние устройства наблюдения, которые зондируют ресурс *за пределами* кластера Service Fabric (например, служба отслеживания Gomez).

> [AZURE.NOTE] По умолчанию кластер заполняется отчетами о работоспособности, отправляемыми компонентами системы. Подробнее об этом читайте в статье [Использование отчетов о работоспособности системы для устранения неполадок](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Отчеты пользователей должны отправляться в [сущности работоспособности](service-fabric-health-introduction.md#health-entities-and-hierarchy), которые уже созданы системой.

Если схема отчетности определена, отправлять отчеты о работоспособности очень просто. Для отправки отчетов о работоспособности можно использовать `FabricClient`, если кластер не является [безопасным](service-fabric-cluster-security.md) или клиент fabric имеет привилегии администратора. Это можно сделать через API с помощью [FabricClient.HealthManager.ReportHealth](https://msdn.microsoft.com/library/system.fabric.fabricclient.healthclient.reporthealth.aspx), через PowerShell или REST. Повысить производительность можно с помощью настройки пакетных отчетов.

> [AZURE.NOTE] Отчеты о работоспособности синхронизированы, и они отображают только проверку на стороне клиента. То, что отчет принимается клиентом работоспособности или объектами `Partition` или `CodePackageActivationContext`, не означает, что он применяется в хранилище. Он будет отправлен асинхронно и, возможно, в пакете с другими отчетами. Поэтому во время обработки на сервере может произойти сбой (например, из-за устаревшего порядкового номера, из-за удаленной сущности, в которой должен применяться отчет, и т. д).

## Клиент работоспособности
Отчеты о работоспособности отправляются в хранилище данных о работоспособности с помощью клиента работоспособности, который находится в клиенте Fabric. Клиент работоспособности можно настроить с помощью приведенных ниже параметров.

- **HealthReportSendInterval**. Задержка между временем добавления отчета в клиент и отправкой в хранилище данных о работоспособности. Она используется для отправки пакетных отчетов одним сообщением вместо отдельных сообщений для каждого отчета. Это повышает производительность. Значение по умолчанию — 30 секунд.

- **HealthReportRetrySendInterval**. Интервал, через который клиент работоспособности повторно отправляет накопленные отчеты о работоспособности в хранилище данных о работоспособности. Значение по умолчанию — 30 секунд.

- **HealthOperationTimeout**. Время ожидания сообщения с отчетом, отправленного в хранилище данных о работоспособности. После истечения времени ожидания клиент работоспособности будет повторять попытки, пока хранилище данных о работоспособности не подтвердит обработку отчета. Значение по умолчанию — две минуты.

> [AZURE.NOTE] Чтобы отправить отчеты, объединенные в пакеты, клиент Fabric должен работать как минимум на протяжении времени, указанного в HealthReportSendInterval. Если сообщение утеряно или хранилищу данных о работоспособности не удалось применить его из-за временных ошибок, клиент Fabric должен работать дольше, чтобы стала возможной повторная попытка.

Буферизация на клиенте выполняется с учетом уникальности отчетов. Например, если какой-либо неисправный информатор отправляет 100 отчетов в секунду для одного свойства одной сущности, будет использоваться последняя версия отчета. В очереди клиента существует только один такой отчет. Если настроена пакетная обработка, в хранилище данных о работоспособности за один интервал отправки будет отправляться только один отчет. Последний добавленный отчет отображает актуальное состояние сущности. Все параметры настройки можно указать при создании `FabricClient`. Для этого необходимо передать параметры [FabricClientSettings](https://msdn.microsoft.com/library/azure/system.fabric.fabricclientsettings.aspx) с нужными значениями в записи, связанные с работоспособностью.

В примере ниже приведено создание клиента Fabric и указано, что отчеты должны отправляться сразу после их добавления. Если при повторных попытках возникают ошибки или превышено время ожидания, попытки повторяются каждые 40 секунд.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Такие же параметры можно задать во время создания подключения к кластеру через PowerShell. Приведенный ниже код запускает подключение к локальному кластеру.

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

> [AZURE.NOTE] Чтобы запретить неавторизованным службам отправлять отчеты о работоспособности сущностям в кластере, на сервере можно настроить прием запросов только от надежных клиентов. Так как отчеты отправляются с помощью `FabricClient`, это означает, что для `FabricClient` необходимо включить защиту, чтобы иметь возможность связываться с кластером (например проверку подлинности Kerberos или на основе сертификата). Ознакомьтесь с дополнительными сведениями о [безопасности кластера](service-fabric-cluster-security.md).

## Создание отчета из служб с низким уровнем привилегий
В службах Service Fabric, которые не имеют административного доступа к кластеру, можно передать сведения о работоспособности для записей из текущего контекста с помощью `Partition` или `CodePackageActivationContext`.

- Для служб без отслеживания состояния используйте [IStatelessServicePartition.ReportInstanceHealth](https://msdn.microsoft.com/library/system.fabric.istatelessservicepartition.reportinstancehealth.aspx), чтобы сформировать отчет о текущем экземпляре службы.

- Для служб с отслеживанием состояния используйте [IStatefulServicePartition.ReportReplicaHealth](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.reportreplicahealth.aspx), чтобы сформировать отчет о текущей реплике.

- Используйте [IServicePartition.ReportPartitionHealth](https://msdn.microsoft.com//library/system.fabric.iservicepartition.reportpartitionhealth.aspx) для создания отчетов о текущей сущности секции.

- Используйте [CodePackageActivationContext.ReportApplicationHealth](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.reportapplicationhealth.aspx) для формирования отчета о текущем приложении.

- Используйте [CodePackageActivationContext.ReportDeployedApplicationHealth](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth.aspx) для создания отчетов о текущем приложении, развернутом на текущем узле.

- Используйте [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth.aspx) для создания отчетов о пакете службы для текущего приложения, развернутого на текущем узле.

> [AZURE.NOTE] На внутреннем уровне `Partition` и `CodePackageActivationContext` содержат клиент работоспособности, который настроен с параметрами по умолчанию. Применяются те же соображения, что и для [клиента работоспособности](service-fabric-report-health.md#health-client) — отчеты объединяются в пакеты и отправляются по таймеру, поэтому объекты должны поддерживаться в рабочем режиме для отправки отчета.

## Создание отчета о работоспособности
Первым шагом в создании высококачественных отчетов является определение условий, которые могут повлиять на работоспособность службы. Если условие может помочь найти проблемы в службе или кластере при их запуске (или, что еще лучше, выявить проблемы до их возникновения), оно потенциально может сохранить миллиарды долларов. Вы получите следующие преимущества: меньшее время простоя, меньше ночей, потраченных на анализ и устранение проблем, и высокое качество обслуживания клиентов.

После задания условий создателям устройств наблюдения нужно выбрать лучший способ отслеживания, чтобы найти баланс между нагрузкой и пользой. Например, рассмотрим службу, которая выполняет сложные вычисления, используя некоторые временные файлы в общей папке. Устройство наблюдения может отслеживать общую папку на наличие в ней достаточного объема свободного места. Оно может отслеживать уведомления об изменении файла или каталога. Устройство наблюдения может также отправлять предупреждение при приближении к пороговому значению и сообщать об ошибке, если общая папка заполнена. Получив предупреждение, система восстановления может запустить очистку старых файлов в общей папке. Получив сообщение об ошибке, система восстановления может переместить реплику службы на другой узел. Обратите внимание, что состояния условий описаны с точки зрения работоспособности: обращается внимание на то, какое состояние условия можно считать работоспособным, а какое нет (предупреждение или ошибка).

После настройки сведений об отслеживании создатель устройства наблюдения должен определить, как реализовать устройство. Если условия можно определить из службы, модуль отслеживания может быть интегрирован в отслеживаемую службу. Например, код службы может проверять использование общей папки и отправлять отчеты с помощью локального клиента Fabric каждый раз, когда он пытается записать файл. Преимущество такого подхода заключается в простоте отчетности. Однако будьте осторожны. Следите за тем, чтобы ошибки в устройстве наблюдения не влияли на работу службы.

Создание отчетов в отслеживаемой службе не всегда целесообразно. Устройство наблюдения в службе может не обнаружить условия. Оно может не иметь логики или данных для их определения. Накладные затраты на отслеживание условий могут быть высокими. Условия могут быть не характерными для службы, а касаться взаимодействия между службами. Другой вариант — использовать устройства наблюдения в кластере как отдельные процессы. Модули наблюдения просто отслеживают условия и отправляют отчеты, никак не затрагивая основные службы. Эти устройства можно реализовать, к примеру, как службы без отслеживания состояния в одном приложении, развернуть на всех узлах или на одних тех же узлах как службу.

В некоторых случаях нецелесообразно использовать устройства наблюдения, выполняющиеся в кластере. Если отслеживаемыми условиями выступает доступность или функциональность службы с точки зрения пользователей, лучше размещать устройства наблюдения там же, где находятся пользовательские клиенты. Тогда они смогут тестировать операции так же, как их вызывают пользователи. Например, у вас может быть устройство наблюдения, которое выходит за пределы кластера и отправляет запросы службе, а затем проверяет задержку и правильность результата. (Например, запрос к службе калькулятора: возвращает ли 2 + 2 результат 4 в разумные сроки?)

Когда разработка устройства наблюдения завершена, выберите идентификатор источника, который однозначно идентифицирует его. Если в кластере имеется несколько устройств наблюдения одинакового типа, они должны отправлять отчеты либо о различных сущностях, либо об одной и той же сущности. Убедитесь, что свойства или идентификаторы источника отличаются. Так отчеты смогут сосуществовать. Свойство отчета о работоспособности должно содержать отслеживаемое условие. (В примере выше это может быть свойство **ShareSize**.) Если к одному условию применяется множество отчетов, в свойстве должны содержаться некоторые динамические сведения, чтобы отчеты могли сосуществовать. Например, при наличии нескольких общих папок, которые нужно отслеживать, имя свойства может быть **ShareSize-имя\_общей\_папки**.

> [AZURE.NOTE] Хранилище данных о работоспособности *нельзя* использовать для хранения сведений о состоянии. В хранилище должны поступать только сведения, связанные с работоспособностью, которые влияют на оценку работоспособности сущности. Хранилище данных о работоспособности не является хранилищем общего назначения. Оно использует логику оценки работоспособности для сбора всех данных в состояние работоспособности. Отправка сведений, не связанных с работоспособностью (например, сообщение с состоянием работоспособности «ОК»), не повлияет на сводное состояние работоспособности, однако может отрицательно повлиять на производительность хранилища данных о работоспособности.

Следующий вопрос: какие сущности включать в отчет. В большинстве случаев это очевидно — выбор делается, исходя из условия. Выберите сущность с наиболее оптимальной степенью детализации. Если условие влияет на все реплики в разделе, отправляется отчет о разделе, а не о службе. Хотя бывают и патологические случаи, когда принять решение сложно. Если условие влияет на сущность (например, реплику), но его нужно помечать дольше, чем жизненный цикл реплики, должен отправляться отчет о разделе. В противном случае после удаления реплики все отчеты, связанные с ней, будут удалены из хранилища. Это означает, что создателям устройств наблюдения следует также подумать о времени существования сущности и отчета. Должно быть понятно, когда отчет нужно удалять из хранилища (например, в случае сообщения об ошибке сущности, которая больше не применяется).

Рассмотрим пример, чтобы лучше понять все описанное выше. У нас есть приложение Service Fabric, которое состоит из главной службы с отслеживанием состояния и дополнительных служб без отслеживания состояния, развернутых на всех узлах (один тип дополнительной службы для одного типа задачи). Главная служба имеет очередь обработки команд, которые выполняются дополнительными службами. Дополнительные службы выполняют полученные запросы и отправляют подтверждения. Может отслеживаться только одно условие — длина очереди обработки главной службы. Если длина очереди главной службы достигает порогового значения, отображается предупреждение. Это означает, что дополнительные службы не могут справиться с нагрузкой. Если очередь достигла максимальной длины и команды удаляются, отображается сообщение об ошибке, так как служба не может восстановить удаленные команды. Отчеты могут содержать сведения о свойстве **QueueStatus**. Устройство наблюдения находится в службе и периодически отправляется в основной реплике главной службы. Срок жизни составляет две минуты с периодической отправкой каждые 30 секунд. Если основная реплика выходит из строя, отчет автоматически удаляется из хранилища. Если реплика службы работает, но заблокирована или имеет другие проблемы, срок хранения отчета в хранилище данных о работоспособности истечет. В таком случае сущность будет считаться ошибочной.

Другое условие, которое можно отслеживать, — время выполнения задачи. Главная служба распределяет задачи для дополнительных служб в зависимости от типа задач. В зависимости от структуры главная служба может опрашивать дополнительные службы о состоянии задач. Кроме этого, она также может ожидать от дополнительных служб подтверждения о завершении. Во втором случае необходимо внимательно следить, чтобы не пропустить ситуации, когда останавливаются дополнительные службы или теряются сообщения. Один из вариантов — отправка главной службой запроса проверки связи одной и той же дополнительной службе для получения ответа о состоянии. Если состояние не получено, главная служба считает, что случился сбой, и переназначает задачу. Это предполагает, что задачи являются идемпотентными.

Мы можем интерпретировать отслеживаемое условие как предупреждение, если задача не выполняется через определенное время **t1** (например, 10 минут), или как ошибку, если задача не будет завершена за время **t2** (например, 20 минут). Отчеты можно отправлять несколькими способами.

- Основная реплика главной службы периодически отправляет отчеты о себе. Вы можете задать одно свойство для всех ожидающих задач в очереди. Если хотя бы одна задача выполняется дольше, следует отправить отчет о свойстве **PendingTasks** с предупреждением или ошибкой соответственно. Если нет ожидающих задач или все они только что запущены, отправьте отчет с состоянием «ОК». Задачи постоянные, поэтому, если главная служба выходит из строя, новая назначенная главная служба продолжает отправлять отчеты надлежащим образом.

- Другой процесс устройства наблюдения (облачный или внешний) проверяет (снаружи, на основе нужного результата задачи) состояние завершения задач. Если они выходят за пределы пороговых значений, отправляется отчет о главной службе. По каждой задаче также отправляется отчет с идентификатором задачи (например, **PendingTask+taskid**). Отчеты должны отправляться только о неработоспособных состояниях. Задайте срок жизни в несколько минут и пометьте отчеты для удаления по истечении срока, чтобы выполнить очистку.

- Дополнительная служба, выполняющая задачу, отправляет отчет, если ее выполнение занимает больше времени, чем ожидалось. Она отправляет отчет об экземпляре службы для свойства **PendingTasks**. В нем выявляет экземпляр службы, который имеет проблемы, но он не включает случаи, когда экземпляр выходит из строя. В это время отчеты будут удалены. Может быть отправлен отчет о дополнительной службе. Если дополнительная служба выполняет задачу, экземпляр службы удаляет отчет из хранилища. В нем не регистрируются случаи, когда подтверждение теряется и задача не выполняется с точки зрения главной службы.

Хотя отчеты отправляются в описанных выше случаях, они будут собираться при оценке работоспособности приложения.

## Периодические отчеты и отчеты на основе переходов
С помощью модели создания отчетов о работоспособности устройства наблюдения могут отправлять отчеты периодически или на основе переходов. Мы рекомендуем выбирать периодическую отчетность для отчетов наблюдения, так как она требует более простого кода и, следовательно, меньше подвержена ошибкам. Во избежание ошибок, которые приводят к неправильным отчетам, устройства наблюдения должны быть максимально простыми. Неправильный отчет о *неработоспособности* влияет на оценку работоспособности и на сценарии, основанные на работоспособности, в частности обновления. Неправильные отчеты о *работоспособности* скрывают проблемы в кластере, а это нежелательно.

Для периодической отчетности модуль наблюдения можно реализовать с таймером. При обратном вызове таймера устройство наблюдения может проверить состояние и отправить отчет на основе текущего состояния. При этом не нужны сведения о том, какой отчет был отправлен ранее, и не нужно оптимизировать что-либо в обмене сообщениями. Клиент работоспособности имеет логику для облегчения пакетной обработки. Пока клиент работоспособности активен, внутри него будут повторяться попытки, пока отчет не подтвердится хранилищем данных о работоспособности или устройство наблюдения не создаст новый отчет с теми же сущностью, свойством и источником.

Отчеты о переходах требуют тщательной обработки состояния. Устройство наблюдения отслеживает некоторые условия и создает отчеты только в случае изменения условий. Преимущество состоит в меньшем количестве отчетов. Недостатком является сложность логики устройства наблюдения. Условия или отчеты также нужно сохранять, чтобы можно было отследить изменения состояний. При отработке отказа нужно уделить внимание отправке отчета, который, возможно, не удалось отправить ранее (поставлен в очередь, но еще не отправлен в хранилище данных о работоспособности). Порядковый номер должен постоянно увеличиваться. В противном случае отчеты будут отклонены из-за устаревания. В некоторых случаях после потерь данных может понадобиться синхронизация состояния информатора с состоянием хранилища данных о работоспособности.

Отчеты на основе переходов имеют смысл, когда служба сообщает данные о своем состоянии с помощью `Partition` или `CodePackageActivationContext`. При удалении локального объекта (реплика, развернутый пакет службы или развернутое приложение) все отчеты для этого объекта также удаляются. Это снижает необходимость синхронизации между создателями отчетов и хранилищем данных о работоспособности. Если отчет предназначен для родительской секции или родительского приложения, необходимо соблюдать осторожность при отработке отказа, чтобы избежать устаревших отчетов в хранилище данных о работоспособности. Необходимо добавить логику для поддержания корректного состояния и удаления отчетов из хранилища, когда они больше не нужны.

## Реализация отчетов о работоспособности
Мы рассмотрели сущности и отчеты. Теперь можно переходить к отправке отчетов о работоспособности с помощью API, PowerShell или REST.

### API
Чтобы отправить отчет через API, нужно создать отчет о работоспособности, соответствующий типу сущности, по которому нужен отчет. Затем отчет необходимо передать клиенту работоспособности. Пользователи также могут создать сведения о работоспособности и передать их в соответствующие методы подготовки отчетов на `Partition` или `CodePackageActivationContext` для создания отчетов о текущих сущностях.

В приведенном ниже примере показано создание периодических отчетов из устройства наблюдения в кластере. Устройство наблюдения проверяет наличие доступа к внешнему ресурсу с узла. Ресурс необходим манифесту службы в приложении. Если ресурс недоступен, другие службы в приложении могут работать неправильно. Следовательно, отчет отправляется в развернутую сущность пакета службы каждые 30 секунд.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### PowerShell
Пользователи могут отправлять отчеты о работоспособности с помощью команды **Send-ServiceFabric*EntityType*HealthReport**.

В следующем примере показано создание периодических отчетов по загрузке ЦП на узле. Отчеты должны отправляться каждые 30 секунд, а срок их жизни составляет две минуты. Если срок хранения истекает, это означает, что возникли проблемы с информатором, поэтому узел будет считаться ошибочным. Если загрузка ЦП превышает пороговое значение, состояние работоспособности в отчете — предупреждение. Если же загрузка ЦП превышает пороговое значение в течение заданного времени, состоянием работоспособности будет ошибка. В противном случае информатор отправляет состояние «ОК».

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

В следующем примере создается временное предупреждение о реплике. Сначала берется идентификатор раздела и реплики для нужной службы. Затем отправляется отчет из **PowershellWatcher** о свойстве **ResourceDependency**. Отчет хранится только две минуты, а затем автоматически удаляется из хранилища.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

## Дальнейшие действия

На основе данных о работоспособности создатели служб и администраторы кластеров или приложений могут решить, как использовать полученные сведения. Например, они могут настроить оповещения на основе состояния работоспособности для выявления серьезных проблем, которые могут вызвать простои. Кроме того, администраторы могут настроить системы восстановления, чтобы устранять неполадки автоматически.

[Общие сведения о наблюдении за работоспособностью системы в Service Fabric](service-fabric-health-introduction.md)

[Просмотр отчетов о работоспособности Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Проверка работоспособности службы и оповещение о проблемах](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Использование отчетов о работоспособности системы для устранения неполадок](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Мониторинг и диагностика состояния служб в локальной среде разработки](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Обновление приложения Service Fabric](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0713_2016-->
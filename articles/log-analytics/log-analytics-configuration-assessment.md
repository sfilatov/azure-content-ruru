<properties
	pageTitle="Решение ";Оценка конфигурации"; в Log Analytics | Microsoft Azure"
	description="Решение ";Оценка конфигурации"; в службе Log Analytics предоставляет подробные сведения о текущем состоянии инфраструктуры серверов System Center Operations Manager при использовании агентов или группы управления Operations Manager."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="banders"/>

# Решение "Оценка конфигурации" в Log Analytics

Решение "Оценка конфигурации" в службе Log Analytics помогает обнаружить потенциальные проблемы конфигурации сервера с помощью оповещений и рекомендаций в базе знаний.

Для работы этого решения требуется System Center Operations Manager. Решение "Оценка конфигурации" недоступно, если используются только непосредственно подключенные агенты.

Для просмотра некоторых сведений в решении "Оценка конфигурации" требуется подключаемый модуль Silverlight для веб-браузера.

>[AZURE.NOTE] Решение "Оценка конфигурации" доступно только для рабочих областей в регионе "Восточная часть США" и не будет добавлено в другие регионы. Функции решения "Оценка конфигурации" заменяются решениями для конкретных рабочих нагрузок, включая оценку SQL Server и оценку Active Directory.

![Элемент "Оценка конфигурации"](./media/log-analytics-configuration-assessment/oms-config-assess-tile.png)

Данные конфигурации собираются из отслеживаемых серверов и затем отправляются в службу OMS в облаке для обработки. К полученным данным применяется логика и облачная служба записывает данные. Обработанные данные для серверов отображаются в следующих областях.

- **Оповещения.** Отображаются относящиеся к конфигурации упреждающие оповещения, которые были созданы для наблюдаемых серверов. Их создают правила, разработанные организацией поддержки пользователей Майкрософт (CSS) на основе рекомендаций с мест.
- **Рекомендации в базе знаний**. Отображаются статьи базы знаний Майкрософт, которые рекомендуются для рабочих нагрузок в вашей инфраструктуре. Они предлагаются автоматически на основе конфигурации в рамках использования машинного обучения.
- **Анализируемые серверы и рабочие нагрузки**. Отображаются серверы и рабочие нагрузки, которые отслеживает служба OMS.

![Панель мониторинга "Оценка конфигурации"](./media/log-analytics-configuration-assessment/oms-config-assess-dash01.png)

### Технологии, которые можно анализировать с помощью решения "Оценка конфигурации"

Решение "Оценка конфигурации" анализирует следующие рабочие нагрузки.

- Windows Server 2012 и Microsoft Hyper-V Server 2012
- Windows Server 2008 R2, Windows Server 2008 и Windows Server 2012 R2.
    - Active Directory
	- Узел Hyper-V
	- Общая операционная система
- SQL Server 2008 и более поздних версий
    - SQL Server Database Engine
- Microsoft SharePoint 2010
- Microsoft Exchange Server 2010 и Microsoft Exchange Server 2013
- Microsoft Lync Server 2013 и Lync Server 2010
- System Center 2012 SP1 — Virtual Machine Manager

Для SQL Server поддерживается анализ следующих 32-разрядных и 64-разрядных выпусков:

- SQL Server 2016 — все выпуски
- SQL Server 2014 — все выпуски
- SQL Server 2008 и 2008 R2 — все выпуски

Во всех поддерживаемых выпусках анализируется компонент SQL Server Database Engine. Кроме того, 32-разрядный выпуск SQL Server поддерживается при работе в режиме реализации WOW64.

## Установка и настройка решения
Для установки и настройки решений используйте указанные ниже данные.

- Operations Manager является обязательным компонентом для работы решения "Оценка конфигурации".
- Агент Operations Manager должен быть установлен на каждом компьютере, для которого требуется выполнять оценку конфигурации.
- Решение "Оценка конфигурации" необходимо добавить в рабочую область OMS, как описано в статье [Добавление решений Log Analytics из каталога решений](log-analytics-add-solutions.md). Дополнительная настройка не требуется.

## Сведения о сборе данных оценки конфигурации

В следующей таблице представлены методы сбора данных и другие сведения о сборе данных для решения "Оценка конфигурации".

| платформа | Direct Agent | Агент SCOM | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | частота сбора |
|---|---|---|---|---|---|---|
|Windows|![Нет](./media/log-analytics-configuration-assessment/oms-bullet-red.png)|![Да](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![Нет](./media/log-analytics-configuration-assessment/oms-bullet-red.png)| ![Да](./media/log-analytics-configuration-assessment/oms-bullet-green.png)|![Да](./media/log-analytics-configuration-assessment/oms-bullet-green.png)| дважды в день|


## Оповещения оценки конфигурации
Просматривать оповещения и управлять ими можно на странице "Оповещения" решения "Оценка конфигурации". Оповещения сообщают об обнаруженной проблеме, ее причинах и способах решения. Они также содержат сведения о параметрах конфигурации в вашей среде, которые могут вызывать проблемы производительности.

![просмотр оповещений](./media/log-analytics-configuration-assessment/oms-config-assess-alerts01.png)

>[AZURE.NOTE] Оповещения решения "Оценка конфигурации" отличаются от других оповещений Log Analytics. Для просмотра оповещений требуется подключаемый модуль Silverlight для веб-браузера.

При выборе элемента или категории элементов на странице оповещений будет выведен список серверов или рабочих нагрузок с оповещениями, которые применяются к каждому элементу.

![список оповещений](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-view-config.png)

Каждое оповещение содержит ссылки на статью базы знаний Майкрософт. В этих статьях содержатся дополнительные сведения об оповещении.

>[AZURE.TIP] По умолчанию на экран выводится не более 2000 оповещений. Чтобы просмотреть дополнительные оповещения, щелкните панель уведомлений над списком оповещений.

Можно щелкнуть любой элемент в списке, чтобы просмотреть статью базы знаний, которая может помочь устранить причину проблемы, вызвавшей оповещение.

![статья базы знаний](./media/log-analytics-configuration-assessment/oms-config-assess-alerts-details-kb.png)

Вы можете управлять правилами оповещений, игнорируя определенные правила или классы правил.

![управление правилами генерации оповещений](./media/log-analytics-configuration-assessment/oms-config-assess-alert-rules.png)

## Рекомендации в базе знаний
При просмотре рекомендаций в базе знаний на экран выводятся результаты поиска в журналах со списком статей базы знаний Майкрософт, рекомендуемых для рабочих нагрузок и компьютеров. Эти статьи содержат дополнительные сведения об оповещениях.

![результаты поиска для рекомендаций в базе знаний](./media/log-analytics-configuration-assessment/oms-config-assess-knowledge-recommendations.png)

## Анализируемые серверы и рабочие нагрузки
При просмотре рекомендаций в базы знаний на экран выводятся результаты поиска в журналах со списком серверов и рабочих нагрузок, данные о которых передаются в службу OMS из Operations Manager.

![Серверы и рабочие нагрузки](./media/log-analytics-configuration-assessment/oms-config-assess-servers-workloads.png)

## Дальнейшие действия

- Используйте [Поиск по журналам в Log Analytics](log-analytics-log-searches.md) для просмотра подробных данных по оценке конфигурации.

<!---HONumber=AcomDC_0518_2016-->
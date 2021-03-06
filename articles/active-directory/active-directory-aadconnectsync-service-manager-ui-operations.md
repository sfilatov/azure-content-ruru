<properties
	pageTitle="Синхронизация Azure AD Connect: пользовательский интерфейс Synchronization Service Manager | Microsoft Azure"
	description="Общие сведения о вкладке ";Operations"; (Операции) в Synchronization Service Manager для Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/07/2016"
	ms.author="andkjell"/>


# Синхронизация Azure AD Connect: Synchronization Service Manager

[Операции](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Соединители](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Конструктор метавселенной](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Поиск в метавселенной](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

На вкладке "Operations" (Операции) отображаются результаты последних операций. Эта вкладка крайне важна для понимания и устранения неполадок.

## Пояснение информации, отображаемой на вкладке "Operations" (Операции)
В верхней части отображены все операции выполнения в хронологическом порядке. По умолчанию журнал операций содержит сведения за последние семь дней, но эту цифру можно изменить с помощью [планировщика](active-directory-aadconnectsync-feature-scheduler.md). Вы можете найти любую операцию выполнения, которая не была завершена успешно. Вы также можете изменить порядок сортировки, щелкая заголовки.

Столбец **Status** (Состояние) является самым важным, он показывает наиболее серьезную проблему при выполнении. Ниже приводится краткая сводка наиболее распространенных состояний в порядке приоритета для анализа (где * означает, что возможно несколько строк ошибки).

Состояние | Комментарий
--- | ---
stopped-* | Не удалось завершить выполнение. Например, если удаленная система не работает и с ней не удается связаться.
stopped-error-limit | Обнаружено более 5000 ошибок. Выполнение было автоматически остановлено из-за большого количества ошибок.
completed-*-errors | Выполнение завершено, но есть ошибки (меньше 5000), которые необходимо изучить.
completed-*-warnings | Запуск завершен, но некоторые данные не находятся в ожидаемом состоянии. Если обнаружены ошибки, такое сообщение обычно является указателем. Пока вы не исправили ошибки, не следует рассматривать предупреждения.
Успешное завершение | Проблемы отсутствуют.

При выборе строки в нижней части отображаются сведения об этой операции выполнения. Слева внизу может отображаться список **Step #** (Шаг №). Он отображается, только если у вас в лесу несколько доменов, где каждый домен представлен шагом. Имя домена можно найти в разделе **Partition** (Секция). В разделе **Synchronization Statistics** (Статистика синхронизации) можно найти дополнительные сведения о числе обработанных изменений. Щелкая ссылки, можно получить список измененных объектов. Если у вас есть объекты с ошибкой, они отображаются в разделе **Synchronization Errors** (Ошибки синхронизации).

## Устранение ошибок на вкладке "Operations" (Операции)
![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png) Если есть ошибки, отображаемый в них объект и сама ошибка представлены ссылками, перейдя по которым можно получить дополнительные сведения.

Для начала щелкните строку ошибки (**sync-rule-error-function-triggered** на рисунке выше). Сначала отображается обзор объекта. Чтобы просмотреть фактическую ошибку, нажмите кнопку **Stack Trace** (Трассировка стека). Эта трассировка отображает информацию уровня отладки об ошибке.

**Совет.** Вы можете щелкнуть правой кнопкой поле **call stack information** (Сведения о стеке вызовов), выбрать пункт **select all** (Выбрать все) и щелкнуть **copy** (Копировать). Затем можно скопировать стек и просмотреть ошибку в любом редакторе на ваше усмотрение, например в Блокноте.

- Если получена ошибка из **SyncRulesEngine**, в начале сведений стека вызовов можно просмотреть список всех атрибутов объекта. Прокрутите вниз до заголовка **InnerException =>**. ![Synchronization Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png) В строке ниже отображается ошибка. На рисунке выше ошибка получена из-за создания пользовательского правила синхронизации Fabrikam.

Если сама ошибка не содержит достаточно сведений, значит, пора взглянуть на данные. Можно щелкнуть ссылку с идентификатором объекта и [следовать за объектом и его данными в системе](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## Дальнейшие действия
Узнайте больше о настройке [службы синхронизации Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0907_2016-->
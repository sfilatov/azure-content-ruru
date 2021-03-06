<properties
	pageTitle="Изменение уровня обслуживания и уровня производительности базы данных SQL Azure"
	description="Измените уровень производительности и уровень обслуживания базы данных SQL Azure для масштабирования базы данных. Изменение ценовой категории базы данных SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Изменение уровня обслуживания и уровня производительности (ценовой категории) базы данных SQL


> [AZURE.SELECTOR]
- [Портал Azure](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Уровни службы и уровни производительности описывают функции и ресурсы, доступные для базы данных SQL. Их можно обновлять в соответствии с потребностями приложения. Дополнительные сведения см. в статье [Уровни служб](sql-database-service-tiers.md).

Обратите внимание, что при изменении уровня обслуживания или уровня производительности базы данных создается реплика исходной базы данных с новым уровнем производительности, после чего устанавливаются подключения к реплике. Во время этого процесса данные не теряются, но в течение короткого периода, когда производится переподключение к реплике, соединения с базой данных становятся неактивны, поэтому некоторые текущие транзакции могут откатиться. Этот период может быть разным, но обычно он не превышает 4 секунд, а в более чем 99 % случаев его длительность составляет менее 30 секунд. В очень редких случаях, особенно если в момент отключения соединений выполняется большое количество транзакций, этот период может быть длиннее.

Длительность всего процесса вертикального масштабирования зависит от размера и уровня службы базы данных до и после изменения. Например, если база данных размером 250 ГБ переводится с уровня службы "Стандартный", на этот уровень или в его пределах, процесс должен завершиться в течение 6 часов. Если уровень производительности базы данных того же размера меняется в пределах уровня службы "Премиум", процесс должен завершиться в течение 3 часов.


Используйте сведения, представленные в статьях [Обновление баз данных SQL уровня Web или Business до новых уровней служб](sql-database-upgrade-server-portal.md) и [Уровни служб и уровни производительности в Базе данных SQL Azure](sql-database-service-tiers.md), для определения уровня служб и уровня производительности, соответствующих Базе данных SQL Azure.

- При снижении уровня базы данных ее размер не должен превышать максимально допустимый размер целевого уровня служб.
- При обновлении базы данных с включенной [георепликацией](sql-database-geo-replication-overview.md) перед обновлением базы данных-источника необходимо сначала обновить базы данных-получатели до требуемого уровня производительности.
- При понижении уровня служб необходимо сначала завершить все активные отношения георепликации.
- Предложения службы восстановления отличаются для разных уровней служб. При переходе на более низкий уровень можно потерять возможность восстановления на момент времени или получить меньший срок хранения резервных копий. Дополнительные сведения см. в статье [Резервное копирование и восстановление Базы данных SQL Azure](sql-database-business-continuity.md).
- Изменение ценовой категории базы данных не приводит к изменению максимального размера баз данных. Чтобы изменить максимальный размер базы данных, используйте [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) или [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- Новые свойства базы данных не применяются до тех пор, пока изменение не завершится.



**Для работы с этой статьей необходимо следующее:**

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
- База данных SQL Azure. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).


## Изменение уровня обслуживания и уровня производительности базы данных


Откройте колонку «База данных SQL» для базы данных, которую необходимо масштабировать.

1.	Перейдите на [портал Azure](https://portal.azure.com).
2.	Нажмите **ПРОСМОТРЕТЬ ВСЕ**.
3.	Нажмите **Базы данных SQL**.
2.	Щелкните базу данных, которую хотите изменить.
3.	В колонке "База данных SQL" выберите **Все настройки**, а затем **Ценовая категория (масштабировать DTU)**.

    ![ценовая категория][1]


1.  Выберите новый уровень обслуживания и нажмите кнопку **Выбрать**.

    После нажатия кнопки **Выбрать** будет отправлен запрос на изменение уровня базы данных. Операция может занять некоторое время в зависимости от размера базы данных. Щелкните уведомление, чтобы просмотреть подробные сведения и состояние операции масштабирования.

    > [AZURE.NOTE] Изменение ценовой категории базы данных не приводит к изменению максимального размера баз данных. Чтобы изменить максимальный размер базы данных, воспользуйтесь [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) или [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![выберите ценовую категорию][2]

3.	На ленте слева щелкните **Уведомления**:

    ![уведомления][3]

## Убедитесь, что ценовая категория база данных соответствует выбранному.

   После завершения операции масштабирования убедитесь, что база данных находится на нужном уровне:

2.	Нажмите **ПРОСМОТРЕТЬ ВСЕ**.
3.	Нажмите **Базы данных SQL**.
2.	Щелкните базу данных, которую вы обновили.
3.	Щелкните **Ценовая категория** и убедитесь, что вы выбрали правильную категорию.

    ![новая цена][4]


## Дальнейшие действия

- Измените максимальный размер базы данных с помощью [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) или [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Масштабирование](sql-database-elastic-scale-get-started.md)
- [Подключение к базе данных SQL и выполнение запросов с помощью SSMS](sql-database-connect-query-ssms.md)
- [Экспорт базы данных SQL Azure](sql-database-export.md)

## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/pricing-tile.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png

<!---HONumber=AcomDC_0720_2016-->
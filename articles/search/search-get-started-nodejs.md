<properties
	pageTitle="Начало работы со службой поиска Azure в NodeJS | Microsoft Azure | Размещенная облачная служба поиска"
	description="Пошаговое создание приложения поиска в облачной службе поиска Azure с использованием NodeJS в качестве языка программирования."
	services="search"
	documentationCenter=""
	authors="EvanBoyle"
	manager="pablocas"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="07/14/2016"
	ms.author="evboyle"/>

# Начало работы с Поиском Azure на NodeJS
> [AZURE.SELECTOR]
- [Портал](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Узнайте, как создать собственное приложение поиска на NodeJS на базе Поиска Azure. В этом руководстве [REST API службы поиска Azure](https://msdn.microsoft.com/library/dn798935.aspx) используется для создания объектов и операций, используемых в этом уроке.

Мы использовали [NodeJS](https://nodejs.org) и NPM, [Sublime Text 3](http://www.sublimetext.com/3) и Windows PowerShell в Windows 8.1 для разработки и проверки этого кода.

Чтобы запустить этот пример, вам потребуется служба поиска Azure. Создать в ней учетную запись можно на [портале Azure](https://portal.azure.com). Пошаговые инструкции приведены в статье [Создание службы поиска Azure на портале](search-create-service-portal.md).

## О данных

В этом примере приложения используются данные [Геологической службы США (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), отфильтрованные по штату Род-Айленд для сокращения размера набора данных. Мы будем использовать эти данные при создании приложения поиска, отображающего в результатах знаковые здания, такие как больницы и школы, а также геологические объекты, например реки, озера и возвышенности.

В этом приложении программа **DataIndexer** создает и загружает индекс с помощью конструкции [индексатора](https://msdn.microsoft.com/library/azure/dn798918.aspx), получая отфильтрованный набор данных USGS из общедоступной Базы данных SQL Azure. Учетные данные и сведения о подключении к сетевому источнику данных предоставляются в программном коде. Дальнейшая настройка не требуется.

> [AZURE.NOTE] Мы применили фильтр к этому набору данных, чтобы не превысить ограничение ценовой категории для бесплатного использования, составляющее 10 000 документов. Если используется стандартная категория, это ограничение не накладывается. Дополнительные сведения об ограничениях каждой ценовой категории см. в статье [Ограничения службы поиска](search-limits-quotas-capacity.md).


<a id="sub-2"></a>
## Поиск имени службы и ключа API службы поиска Azure

Создав службу, вернитесь на портал, чтобы получить URL-адрес или `api-key`. Для подключения к службе поиска вам потребуется как URL-адрес, так и `api-key`, чтобы проверить подлинность при вызове.

1. Войдите на [портал Azure](https://portal.azure.com).
2. На панели переходов щелкните **Служба поиска**, чтобы получить список всех служб поиска Azure, подготовленных для вашей подписки.
3. Выберите службу, которую вы хотите использовать.
4. На панели мониторинга службы вы увидите плитки, указывающие на важную информацию, а также значок ключа для доступа к ключам администратора.

  	![][3]

5. Скопируйте URL-адрес службы, ключ администратора и ключ запроса. Все эти три элемента потребуются вам позже, при добавлении их в файл config.js.

## Загрузка примеров файлов

Загрузить пример можно одним из следующих способов.

1. Перейдите в раздел [AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodeJSIndexerDemo).
2. Нажмите кнопку **Загрузить ZIP-файл**, сохраните ZIP-файл, после чего извлеките все содержащиеся в нем файлы.

Все последующие изменения и операторы выполнения будут применяться к файлам в этой папке.


## Добавление URL-адреса службы поиска и ключа API в файл config.js

С помощью ранее скопированного URL-адреса и ключа API укажите URL-адрес, ключ администратора и ключ запроса в файле конфигурации.

Ключи администратора предоставляют полный контроль над операциями службы, включая создание или удаление индекса и загрузку документов. Напротив, ключи запросов предназначены только для операций чтения, которые, как правило, используются клиентскими приложениями, подключающимися к Поиску Azure.

В этом примере мы добавили ключ запроса, чтобы закрепить лучший метод использования ключа запроса в клиентских приложениях.

На следующем снимке экрана показан файл **config.js**, открытый в текстовом редакторе, с обозначенными соответствующими записями, чтобы вы могли видеть места в файле, в которые необходимо добавить значения, допустимые для вашей службы поиска.

![][5]


## Размещение среды выполнения для примера

Для примера требуется HTTP-сервер, который вы можете установить глобально с помощью npm.

Выполните следующие команды в окне PowerShell.

1. Перейдите в папку, содержащую файл **package.json**.
2. Введите `npm install`.
2. Введите `npm install -g http-server`.

## Создание индекса и запуск приложения

1. Введите `npm run indexDocuments`.
2. Введите `npm run build`.
3. Введите `npm run start_server`.
4. В браузере откройте страницу `http://localhost:8080/index.html`

## Поиск по данным USGS

Набор данных USGS включает записи, относящиеся к штату Род-Айленд. Если нажать кнопку **Поиск** в пустом поле поиска, вы получите 50 первых записей. Это настройка по умолчанию.

Если ввести условие поиска, поисковая система будет искать конкретные записи. Попробуйте ввести региональное имя или название. "Роджер Уильямс" был первым губернатором Род-Айленда. В его честь названы многие парки, здания и школы.

![][9]

Вы также можете попробовать одно из следующих условий поиска:

- Потакет
- Пемброк
- гусь +мыс


## Дальнейшие действия

Это первый учебник по Поиску Azure на базе NodeJS и набора данных USGS. Мы будем постепенно расширять это руководство, чтобы рассказать о дополнительных функциях поиска, которые вы можете использовать в собственных решениях.

Если у вас уже есть опыт работы с Поиском Azure, вы можете использовать этот пример как фундамент для средств подбора (запросы опережающего ввода или автозаполнения), фильтров и фасетной навигации. Вы также можете улучшить страницу результатов поиска, добавив счетчики и пакетную обработку документов, чтобы пользователи могли просматривать результаты постранично.

Новичок в Поиске Azure? Мы рекомендуем ознакомиться с другими учебниками, чтобы получить представление о том, что вы можете создать. Посетите нашу [страницу документации](https://azure.microsoft.com/documentation/services/search/), чтобы найти дополнительные ресурсы. Вы также можете перейти по ссылкам в нашем [списке видео и учебников](search-video-demo-tutorial-list.md), чтобы получить дополнительные сведения.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png

<!---HONumber=AcomDC_0720_2016-->
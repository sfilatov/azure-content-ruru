<properties
    pageTitle="Отправка запросов в индекс службы поиска Azure с помощью пакета SDK для .NET | Microsoft Azure | Размещенная облачная служба поиска"
    description="В службе поиска Azure можно создавать поисковые запросы и с помощью параметров поиска фильтровать, сортировать и уточнять результаты."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# Отправка запросов в индекс службы поиска Azure с помощью пакета SDK для .NET
> [AZURE.SELECTOR]
- [Обзор](search-query-overview.md)
- [Портал](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

В этой статье показано, как отправлять запросы в индекс с помощью [пакета SDK .NET для службы поиска Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Прежде чем приступать к выполнению инструкций из этого руководства, необходимо [создать индекс службы поиска Azure](search-what-is-an-index.md) и [заполнить его данными](search-what-is-data-import.md).

Все приведенные здесь примеры кода написаны на языке C#. Полный исходный код можно найти на сайте [GitHub](http://aka.ms/search-dotnet-howto).

## 1\. Определение ключа API запроса службы поиска Azure
Теперь, когда вы создали индекс службы поиска Azure, вы почти готовы отправлять запросы с помощью пакета SDK для .NET. Для этого сначала нужно получить один из ключей API запроса, созданный для подготовленной службы поиска. Пакет SDK для .NET отправляет этот ключ при каждом запросе к службе. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

1. Чтобы найти ключи API своей службы, войдите на [портал Azure](https://portal.azure.com/).
2. Перейдите к колонке службы поиска Azure.
3. Щелкните значок "Ключи".

Ваша служба получит *ключи администратора* и *ключи запросов*.

  - Первичные и вторичные *ключи администратора* предоставляют полный доступ ко всем операциям, включая возможность управлять службой, создавать и удалять индексы, индексаторы и источники данных. Ключей два, поэтому вы можете и дальше использовать вторичный ключ, если решите повторно создать первичный ключ, и наоборот.
  - *Ключи запросов* предоставляют только разрешение на чтение индексов и документов; обычно они добавляются в клиентские приложения, которые создают запросы на поиск.

Для отправки запросов в индекс можно использовать один из ключей запросов. Для запросов можно использовать также ключи администратора, но в коде приложения следует использовать ключ запроса, так как этот вариант лучше соответствует [принципу предоставления минимальных прав](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## 2\. Создание экземпляра класса SearchIndexClient
Чтобы отправлять запросы с помощью пакета SDK .NET для службы поиска Azure, нужно создать экземпляр класса `SearchIndexClient`. Этот класс имеет несколько конструкторов. Нужный вам конструктор принимает в качестве параметров имя службы поиска, имя индекса и объект `SearchCredentials`. `SearchCredentials` содержит ключ API.

Приведенный ниже код создает новый класс `SearchIndexClient` для индекса hotels, используя значения имени службы поиска и ключа API, которые хранятся в файле конфигурации приложения (`app.config` или `web.config`). Инструкции по созданию индекса hotels см. в статье [Создание индекса службы поиска Azure с помощью пакета SDK для .NET](search-create-index-dotnet.md).

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

У класса `SearchIndexClient` есть свойство `Documents`. Это свойство предоставляет все методы, которые требуются для отправки запросов в индексы службы поиска Azure.

## 3\. Отправка запроса в индекс
Поиск с помощью пакета SDK для .NET сводится к вызову метода `Documents.Search` для класса `SearchIndexClient`. Этот метод принимает несколько параметров, включая текст поиска и объект `SearchParameters`, с помощью которого можно уточнить запрос.

#### Типы запросов
Два основных [типа запросов](search-query-overview.md#types-of-queries), которые вы будете использовать, — это `search` и `filter`. Запрос `search` выполняет поиск по одному или нескольким условиям во всех _поддерживающих поиск_ полях индекса. Запрос `filter` оценивает логическое выражение во всех _фильтруемых_ полях индекса.

Поиск и фильтрация выполняются с помощью метода `Documents.Search`. Поисковый запрос можно передать в параметре `searchText`, а выражение фильтра — в свойстве `Filter` класса `SearchParameters`. Чтобы выполнить фильтрацию и при этом не выполнять поиск, в качестве значения параметра `searchText` укажите `"*"`. Чтобы выполнить поиск, не прибегая к фильтрации, не задавайте значение свойства `Filter` или вообще не передавайте его в экземпляр `SearchParameters`.

#### Примеры запросов

В следующем примере кода показано несколько разных способов отправки запросов в индекс hotels, описанный в статье [Создание индекса службы поиска Azure с помощью пакета SDK для .NET](search-create-index-dotnet.md#DefineIndex). Обратите внимание, что документы, возвращаемые в результатах поиска, — это экземпляры класса `Hotel`, описанного в статье [Импорт данных в службу поиска Azure с помощью пакета SDK для .NET](search-import-data-dotnet.md#HotelClass). Пример кода выводит результаты поиска на консоль с помощью метода `WriteDocuments`. Этот метод описан в следующем разделе.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## 4\. Обработка результатов поиска
Метод `Documents.Search` возвращает объект `DocumentSearchResult`, содержащий результаты запроса. В примере из предыдущего раздела результаты поиска выводятся на консоль с помощью метода `WriteDocuments`.

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Вот как будут выглядеть результаты запросов из предыдущего раздела (если индекс hotels заполнен демонстрационными данными, как описано в статье [Импорт данных в службу поиска Azure с помощью пакета SDK для .NET](search-import-data-dotnet.md)).

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

Приведенный выше пример кода выводит результаты поиска в консоль. Таким же образом вам нужно будет показывать результаты поиска и в вашем приложении. Сведения о том, как отображать результаты поиска в веб-приложении ASP.NET с использованием схемы MVC, см. в [этом примере на сайте GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample).

<!---HONumber=AcomDC_0831_2016-->
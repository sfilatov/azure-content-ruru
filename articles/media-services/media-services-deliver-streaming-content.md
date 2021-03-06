<properties 
	pageTitle="Публикация содержимого служб мультимедиа Azure с помощью .NET" 
	description="Узнайте, как создать указатель для создания URL-адреса потоковой передачи. Примеры кода написаны на языке C# и используют пакет SDK служб мультимедиа для .NET." 
	authors="juliako" 
	manager="erikre" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016"
	ms.author="juliako"/>


# Публикация содержимого служб мультимедиа Azure с помощью .NET
 
> [AZURE.SELECTOR]
- [REST](media-services-rest-deliver-streaming-content.md)
- [.NET](media-services-deliver-streaming-content.md)
- [Портал](media-services-portal-publish.md)

##Обзор

Вы можете осуществлять потоковую передачу набора MP4-файлов с адаптивной скоростью, создавая указатель потоковой передачи OnDemand и формируя URL-адрес потоковой передачи. В разделе, посвященном [кодированию ресурса](media-services-encode-asset.md), описано кодирование данных в набор MP4-файлов с переменной скоростью.

>[AZURE.NOTE]Если содержимое шифруется, то перед созданием указателя настройте политику доставки ресурсов-контейнеров (как описано в [этой](media-services-dotnet-configure-asset-delivery-policy.md) статье).

Можно также использовать указатель потоковой передачи OnDemand, чтобы формировать URL-адреса, указывающие на MP4-файлы для последовательного скачивания.

В этом разделе показано, как создать указатель потоковой передачи OnDemand, чтобы опубликовать ресурс-контейнер и сформировать URL-адреса потоковой передачи Smooth, MPEG DASH и HLS. В нем также показывается, как создать URL-адреса последовательного скачивания.
  	 
##Создание указателя потоковой передачи OnDemand

Чтобы создать указатель потоковой передачи OnDemand и получить URL-адреса, вам нужно выполнить следующее:

   1. Если содержимое шифруется, установите политику доступа.
   2. создать указатель потоковой передачи OnDemand.
   3. Если планируется потоковая передача, получите файл манифеста потоковой передачи (ISM-файл) в ресурсе.
   		
	Если вы планируете последовательное скачивание, получите имена MP4-файлов в ресурсе.
   4. Создайте URL-адрес файла манифеста или URL-адреса MP4-файлов.
   

###Использование пакета SDK служб мультимедиа для .NET 

Создание URL-адресов потоковой передачи

	private static void BuildStreamingURLs(IAsset asset)
	{
	
	    // Create a 30-day readonly access policy. 
      	// You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
	    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
	        TimeSpan.FromDays(30),
	        AccessPermissions.Read);
	
	    // Create a locator to the streaming content on an origin. 
	    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
	        policy,
	        DateTime.UtcNow.AddMinutes(-5));
	
	    // Display some useful values based on the locator.
	    Console.WriteLine("Streaming asset base path on origin: ");
	    Console.WriteLine(originLocator.Path);
	    Console.WriteLine();
	
	    // Get a reference to the streaming manifest file from the  
	    // collection of files in the asset. 
	    var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
	                                EndsWith(".ism")).
	                                FirstOrDefault();
	    
	    // Create a full URL to the manifest file. Use this for playback
	    // in streaming media clients. 
	    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
	    Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
	    Console.WriteLine(urlForClientStreaming);
	    Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
	    Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
	    Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
	    Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
	    Console.WriteLine();
	}

Код выведет следующее:
	
	URL to manifest for client streaming using Smooth Streaming protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
	URL to manifest for client streaming using HLS protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
	URL to manifest for client streaming using MPEG DASH protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)
	

>[AZURE.NOTE]Также по SSL-подключению можно выполнять потоковую передачу содержимого. Для этого убедитесь, что URL-адреса потоковой передачи начинаются с HTTPS.

Создание URL-адресов последовательного скачивания

	private static void BuildProgressiveDownloadURLs(IAsset asset)
	{
	    // Create a 30-day readonly access policy. 
	    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
	        TimeSpan.FromDays(30),
	        AccessPermissions.Read);
	
	    // Create an OnDemandOrigin locator to the asset. 
	    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
	        policy,
	        DateTime.UtcNow.AddMinutes(-5));
	
	    // Display some useful values based on the locator.
	    Console.WriteLine("Streaming asset base path on origin: ");
	    Console.WriteLine(originLocator.Path);
	    Console.WriteLine();
	
	    // Get MP4 files.
	    IEnumerable<IAssetFile> mp4AssetFiles = asset
	        .AssetFiles
	        .ToList()
	        .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));
	            
	    // Create a full URL to the MP4 files. Use this to progressively download files.
	    foreach (var pd in mp4AssetFiles)
	        Console.WriteLine(originLocator.Path + pd.Name);
	}

Код выведет следующее:
	
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4
	
	. . . 

###Использование расширения пакета SDK служб мультимедиа для .NET

Следующий код вызывает методы расширения пакета SDK для .NET, которые создают указатель и формируют URL-адреса Smooth Streaming, HLS и MPEG-DASH для адаптивной потоковой передачи.

	// Create a loctor.
	_context.Locators.Create(
	    LocatorType.OnDemandOrigin,
	    inputAsset,
	    AccessPermissions.Read,
	    TimeSpan.FromDays(30));
	
	// Get the streaming URLs.
	Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
	Uri hlsUri = inputAsset.GetHlsUri();
	Uri mpegDashUri = inputAsset.GetMpegDashUri();
	
	Console.WriteLine(smoothStreamingUri);
	Console.WriteLine(hlsUri);
	Console.WriteLine(mpegDashUri);


##Схемы обучения работе со службами мультимедиа

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Отзывы

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##См. также

[Загрузка ресурсов](media-services-deliver-asset-download.md) [Настройка политики доставки для ресурса-контейнера](media-services-dotnet-configure-asset-delivery-policy.md)

<!---HONumber=AcomDC_0831_2016-->
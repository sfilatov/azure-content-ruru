<properties
    pageTitle="Создание мобильного приложения для Android в службе мобильных приложений Azure | Microsoft Azure"
    description="Изучите этот учебник, чтобы начать работу с серверными частями мобильных приложений Azure для разработки программ на платформе Android."
    services="app-service\mobile"
    documentationCenter="android"
    authors="RickSaling"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="08/17/2016"
    ms.author="ricksal"/>

#Создание приложения Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Обзор

В этом учебнике рассказывается, как добавить облачную серверную службу в мобильное приложение для платформы Android с помощью серверной части мобильного приложения Azure. Мы создадим новую серверную часть мобильного приложения и простое приложение _списка дел_ для Android, которое хранит свои данные в Azure.

Выполнение инструкций из этого учебника необходимо для работы с другими учебниками по Android, посвященными использованию функции мобильных приложений в службе приложений Azure.

## Предварительные требования

Для работы с этим учебником требуется:

* [Средства разработчика Android](https://developer.android.com/sdk/index.html), которые включают интегрированную среду разработки Android Studio и новейшую платформу Android.
* Пакет SDK для мобильных приложений Android в Azure, который автоматически включается как часть скачиваемого вами ознакомительного проекта.
* [Активная учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/).

## Создание серверной части мобильного приложения Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Настройка серверного проекта

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Скачивание и запуск приложения для Android

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_0824_2016-->
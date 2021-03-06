<properties
   pageTitle="Устранение неполадок клиента Docker в Windows с помощью Visual Studio | Microsoft Azure"
   description="Устранение неполадок, которые возникают при использовании Visual Studio для создания и развертывания веб-приложений в Docker в Windows с помощью Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/18/2016"
   ms.author="allclark" />

# Устранение неполадок при разработке в Visual Studio Docker

При работе с инструментами Visual Studio для предварительной версии Docker могут возникать некоторые проблемы из-за природы предварительной версии. Ниже приведены некоторые распространенные проблемы и способы их устранения.

##Не удалось настроить файл Program.cs для поддержки Docker

При добавлении поддержки Docker требуется добавить `.UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_URLS"))` в класс WebHostBuilder(). Если функция `Main()` или новый класс WebHostBuilder отсутствуют в файле `Program.cs`, отображается соответствующее предупреждение. Чтобы сервер Kestrel при запуске в контейнере Docker мог прослушивать не только компьютер localhost, но и входящий трафик, необходимо добавить `.UseUrls()`. После завершения типичный код выглядит примерно так:

```
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_URLS") ?? String.Empty)
            .UseKestrel()
            .UseContentRoot(Directory.GetCurrentDirectory() ?? "")
            .UseIISIntegration()
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

UseUrls() настроил WebHost на прослушивание входящего URL-трафика. [Инструменты Docker для Visual Studio](http://aka.ms/DockerToolsForVS) настроят переменную среды в режиме dockerfile.debug/release следующим образом:

```
# Configure the listening port to 80
ENV ASPNETCORE_SERVER.URLS http://*:80
```

## Не работает сопоставление тома
Чтобы работали функции редактирования и обновления, для совместного использования исходного кода проекта в папке .app в контейнере настроено сопоставление тома. Когда файлы изменяются на хост-компьютере, каталог контейнеров /app использует тот же каталог. В файле docker-compose.debug.yml сопоставление тома включается следующей конфигурацией:

```
volumes:
    - ..:/app
```

Чтобы проверить, работает ли сопоставление тома, попробуйте следующую команду:

**В ОС Windows**

```
docker run -it -v /c/Users/Public:/wormhole busybox
/ # ls
```

Вы должны увидеть список каталогов из папки Users/Public. Если файлы не отображаются и папка /c/Users/Public не пустая, значит сопоставление тома настроено неправильно.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Перейдите в каталог wormhole, чтобы просмотреть содержимое каталога `/c/Users/Public`:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     a.txt
Documents        Libraries        Pictures         desktop.ini
/wormhole #
```

> [AZURE.NOTE] При работе с виртуальными машинами Linux в файловой системе контейнера учитывается регистр.

Если не удается просмотреть содержимое, выполните следующие действия:

**Бета-версия Docker для Windows**
- Убедитесь, что классическое приложение Docker для Windows запущено. Для этого найдите значок `moby` в области уведомлений и убедитесь, что он имеет белый цвет и работает.
- Убедитесь, что сопоставление тома настроено, щелкнув правой кнопкой мыши значок `moby` в области уведомлений и выбрав в параметрах пункт **Manage shared drives** (Управление общими дисками).

**Панель элементов Docker Toolbox с VirtualBox**

По умолчанию VirtualBox предоставляет совместный доступ к папке `C:\Users` под именем `c:/Users`. По возможности переместите свой проект на уровень ниже этого каталога. Можно также вручную добавить нужную папку в список [общих папок](https://www.virtualbox.org/manual/ch04.html#sharedfolders) VirtualBox.
	
##Сборка: не удалось создать образ. Ошибка проверки TLS-подключения: узел не работает

- Убедитесь, что узел Docker по умолчанию работает. См. статью о [настройке клиента Docker](./vs-azure-tools-docker-setup.md).

##Назначение Microsoft Edge браузером по умолчанию

При использовании обозревателя Microsoft Edge сайт может не открываться из-за того, что Edge считает IP-адрес небезопасным. Для исправления ситуации выполните указанные ниже действия.

1. Выберите **Свойства браузера**.
    - В Windows 10 для этого в поле "Выполнить" можно ввести `Internet Options`.
    - В обозревателе Internet Explorer перейдите в меню **Параметры** и выберите пункт **Свойства браузера**.
1. Выберите появившийся пункт **Свойства браузера**.
1. Откройте вкладку **Безопасность**.
1. Выберите зону **Местная интрасеть**.
1. Выберите **Сайты**.
1. Добавьте IP-адрес виртуальной машины (в данном случае узел Docker) в список.
1. Обновите страницу в Edge — сайт заработает.
1. Дополнительные сведения об этой проблеме см. в записи [Microsoft Edge can't see or open VirtualBox-hosted local web sites](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx) (Microsoft Edge не видит или не открывает локальные веб-сайты в VirtualBox) в блоге Скотта Хансельмана (Scott Hanselman).

##Устранение неполадок, версия 0.15 или более ранняя


###При запуске приложения PowerShell открывается, отображает ошибку и закрывается. Страница браузера не открывается.

Сбой при открытии в браузере может означать ошибку на этапе `docker-compose-up`. Чтобы увидеть ошибку, выполните следующие действия:

1. Откройте файл `Properties\launchSettings.json`.
1. Найдите запись Docker.
1. Найдите строку, которая начинается следующим образом:

    ```
    "commandLineArgs": "-ExecutionPolicy RemoteSigned …”
    ```
	
1. Добавьте параметр `-noexit`, чтобы строка имела следующий вид: Теперь окно PowerShell останется открытым, и вы сможете увидеть ошибку.

    ```
	"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”
    ```

<!---HONumber=AcomDC_0824_2016-->
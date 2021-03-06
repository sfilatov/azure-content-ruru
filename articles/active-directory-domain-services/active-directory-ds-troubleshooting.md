<properties
	pageTitle="Предварительная версия доменных служб Azure Active Directory: руководство по устранению неполадок | Microsoft Azure"
	description="Руководство по устранению неполадок для доменных служб Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Доменные службы Azure AD *(Предварительная версия)*. Устранение неполадок
В этой статье приводятся советы по устранению проблем, с которыми вы можете столкнуться при настройке или администрировании доменных служб Azure Active Directory (AD).


### Не удается включить доменные службы Azure AD для каталога Azure AD
Если попытка включить доменные службы Azure AD для каталога завершается сбоем или переключатель возвращается в положение "Отключено", выполните следующие действия.

- Убедитесь, что в этой виртуальной сети нет домена с таким же доменным именем. Например, предположим, что в выбранной виртуальной сети уже есть домен с именем contoso.com, а вы пытаетесь включить в ней управляемый домен доменных служб Azure AD с таким же доменным именем. В таком случае попытка включить доменные службы Azure AD приведет к ошибке. Причина заключается в конфликте доменных имен в этой виртуальной сети. В такой ситуации для настройки управляемого домена доменных служб Azure AD необходимо использовать другое имя. Кроме того, можно отменить подготовку существующего домена и включить доменные службы Azure AD.

- Проверьте, есть ли в каталоге Azure AD приложение с именем Azure AD Domain Services Sync. Если такое приложение есть, его нужно удалить, а затем повторно включить доменные службы Azure AD. Выполните следующие действия, чтобы проверить наличие приложения и удалить его при необходимости.

  1. Перейдите на **портал управления Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. На панели слева выберите **Active Directory**.
  3. Выберите клиента Azure AD (каталог), для которого требуется включить доменные службы Azure AD.
  4. Откройте вкладку **Приложения**.
  5. В раскрывающемся списке выберите пункт **Приложения, которыми владеет моя компания**.
  6. Проверьте наличие приложения с именем **Azure AD Domain Services Sync**. Если приложение есть, удалите его.
  7. Удалив приложение, попробуйте еще раз включить доменные службы Azure AD.


### Пользователи не могут войти в домен, находящийся под управлением доменных служб Azure AD
Если вы столкнулись с ситуацией, при которой один или несколько пользователей клиента Azure AD не могут войти в созданный управляемый домен, выполните следующие действия по устранению неполадок.

- Убедитесь, что [включена синхронизация паролей](active-directory-ds-getting-started-password-sync.md) в соответствии с шагами, описанными на странице «Приступая к работе».

- **Внешние учетные записи**. Убедитесь, что учетная запись пользователя не является внешней учетной записью клиента Azure AD. К примерам внешних учетных записей относятся учетные записи Microsoft (например, joe@live.com) или учетные записи из внешнего каталога Azure AD. Так как у доменных служб Azure AD нет учетных данных для этих учетных записей, эти пользователи не могут войти в управляемый домен.

- **Слишком длинный префикс имени участника-пользователя**. Убедитесь, что префикс имени участника-пользователя затронутой учетной записи (т. е. первая часть имени участника-пользователя) в клиенте Azure AD содержит меньше 20 знаков. Например, в имени участника-пользователя joereallylongnameuser@contoso.com префикс (joereallylongnameuser) содержится больше 20 знаков, и эта учетная запись будет недоступна в домене Azure, управляемом доменными службами AD.

- **Дублирующийся префикс имени участника-пользователя**. Убедитесь, что в клиенте Azure AD нет других учетных записей пользователей с тем же префиксом имени участника-пользователя (т. е. первой частью имени участника-пользователя), как у соответствующей учетной записи пользователя. Например, при наличии двух учетных записей: joeuser@finance.contoso.com и joeuser@engineering.contoso.com, оба пользователя могут столкнуться с проблемами при входе в управляемый домен. Это также может произойти, если одна из учетных записей пользователей является внешней (например joeuser@live.com). Мы работаем над решением этой проблемы.

- **Синхронизированные учетные записи**. Если учетные записи пользователей синхронизируются из локального каталога, убедитесь в следующем.
    - Вы развернули или установили [последнюю рекомендуемую версию Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).

    - Вы включили в Azure AD Connect [выполнение полной синхронизации](active-directory-ds-getting-started-password-sync.md).

    - В зависимости от размера каталога на доступность учетных записей пользователей и хэшей учетных данных в доменных службах Azure AD может потребоваться некоторое время. Убедитесь, что вы подождали достаточно долго перед повторной попыткой аутентификации (в зависимости от размера каталога — от нескольких часов до одного-двух дней).

    - Если проблема повторится после выполнения описанных выше действий, попытайтесь перезапустить службу Microsoft Azure AD Sync. На компьютере синхронизации откройте командную строку и выполните следующие команды.
      1. net stop 'Microsoft Azure AD Sync'
      2. net start 'Microsoft Azure AD Sync'

- **Учетные записи только в облаке**. Если учетная запись пользователя существует только в облаке, убедитесь, что пользователь изменил пароль после включения доменных служб Azure AD. Этот шаг вызывает создание сверток учетных данных, необходимых для доменных служб Azure AD.


### Свяжитесь с нами
Чтобы [оставить отзыв или обратиться за помощью](active-directory-ds-contact-us.md), свяжитесь с командой доменных служб Azure Active Directory.

<!---HONumber=AcomDC_0706_2016-->
<properties
	pageTitle="Azure AD Connect в Microsoft Cloud для Германии"
	description="Azure AD Connect интегрирует локальные каталоги с Azure Active Directory. Таким образом вы сможете предоставить пользователям возможность получать доступ с использованием одного удостоверения для Office 365, Azure и SaaS, интегрированных с Azure AD."
    keywords="введение в Azure AD Connect, обзор Azure AD Connect, что такое Azure AD Connect, установка Аctive Directory, Германия, Шварцвальд"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/08/2016"
	ms.author="billmath"/>

#Общедоступная предварительная версия Azure AD Connect в Microsoft Cloud для Германии

## Введение
Azure AD Connect обеспечивает синхронизацию локальных каталогов Active Directory с каталогами Azure Active Directory. В настоящее время многие сценарии в [Microsoft Cloud для Германии](https://www.microsoft.com/de-de/cloud/deutschland/default.aspx) должны выполняться оператором. При использовании Microsoft Cloud для Германии важно придерживаться следующих рекомендаций.


- Для успешного выполнения синхронизации указанные ниже URL-адреса должны быть открыты через прокси-сервер:
	- *.microsoftonline.de
	- *.windows.net
	- + списки отзыва сертификатов.

- При входе в каталог Azure AD необходимо использовать учетную запись в домене onmicrosoft.de.
- Следующие функции сейчас не поддерживаются:
	- Azure AD Connect Health,
	- автоматические обновления,
	- обратная запись паролей.

## Загрузка
Вы можете загрузить Azure AD Connect из колонки Azure AD Connect на портале. Чтобы найти колонку Azure AD Connect, следуйте указанным ниже инструкциям.

### Колонка Azure AD Connect

Войдите на портал Azure и выполните следующие действия:

1. Щелкните "Обзор".
2.	Выберите Azure Active Directory.
3.	Затем выберите Azure AD Connect.

Вы увидите следующее:

![Колонка Azure AD Connect](media\active-directory-aadconnect-germany\germany1.png)

 
Следующая таблица содержит описание функций, представленных в колонке.


Название|Описание|
----- | ----- |
Состояние синхронизации|Сообщает, включена ли синхронизация.|
Последняя синхронизация|Показывает, когда в последний раз была успешно выполнена синхронизация.|
Федеративные домены|Показывает текущее количество настроенных федеративных доменов.|


## Установка
Чтобы установить Azure AD Connect, воспользуйтесь [этой документацией](active-directory-aadconnect.md#install-azure-ad-connect).

## Дополнительные функции и дополнительные сведения
Для начала ознакомьтесь со статьей [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md), чтобы получить подробные сведения и рекомендации по пользовательским и расширенным настройкам. На этой странице представлена необходимая информация и ссылки на дополнительные инструкции.

<!---HONumber=AcomDC_0914_2016-->
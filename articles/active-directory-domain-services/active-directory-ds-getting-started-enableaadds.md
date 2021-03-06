<properties
	pageTitle="Доменные службы Azure AD: включение доменных служб Azure AD | Microsoft Azure"
	description="Приступая к работе с доменными службами Azure Active Directory (предварительная версия)"
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
	ms.topic="get-started-article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Доменные службы Azure AD *(предварительная версия)* — включение доменных служб Azure AD

## Задача 3. Включение доменных служб Azure AD
На этом шаге мы включим доменные службы Azure AD для каталога. Для включения доменных служб Azure AD для каталога выполните следующие этапы настройки.

1. Перейдите на **классический портал Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. На панели слева выберите **Active Directory**.

3. Выберите клиента Azure AD (каталог), для которого требуется включить доменные службы Azure AD.

    ![Выбор каталога Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Откройте вкладку **Настройка**.

    ![Вкладка "Настройка" каталога](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Прокрутите список до раздела под названием **Доменные службы**.

    ![Раздел настройки доменных служб](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Переключите параметр **Включить доменные службы для этого каталога** в значение **ДА**. Вы увидите, что на странице появились несколько дополнительных параметров конфигурации для доменных служб Azure AD.

    ![Включение доменных служб](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] При включении доменных служб Azure AD для вашего клиента Azure AD создаст и сохранит хэши учетных данных Kerberos и NTLM, необходимые для проверки подлинности пользователей.

7. Укажите **Имя домена DNS для доменных служб**.

   - По умолчанию будет выбрано имя домена для каталога (т. е. имя домена, заканчивающееся суффиксом домена **.onmicrosoft.com**).

   - В списке перечислены все домены, которые были настроены для вашего каталога Azure AD, включая проверяемые и непроверяемые домены, которые были настроены на вкладке «Домены».

   - Кроме того, в этот список можно добавить пользовательское имя домена, введя его. В этом примере в качестве пользовательского имени домена мы указали contoso100.com.

     > [AZURE.WARNING] Убедитесь, что длина указанного префикса доменного имени (например, contoso100 для доменного имени contoso100.com) не превышает 15 символов. Если префикс доменного имени превышает 15 символов, создать домен доменных служб Azure AD нельзя.

8. Следующий шаг — выбор виртуальной сети, в которой должны быть доступны доменные службы Azure AD. Выберите созданную виртуальную сеть в раскрывающемся списке **Подключить доменные службы к этой виртуальной сети**.

   - Убедитесь, что указанная виртуальная сеть принадлежит региону Azure, который поддерживается доменными службами Azure AD.

   - На странице [служб Azure по регионам](https://azure.microsoft.com/regions/#services/) можно узнать, в каких регионах Azure доступны доменные службы Azure AD.

   - Обратите внимание: если виртуальные сети находятся в регионе, в котором не поддерживаются доменные службы Azure AD, эти сети не будут отображаться в раскрывающемся списке.

   - Также виртуальные сети, созданные с помощью Azure Resource Manager (виртуальные сети на основе ARM), не будут отображаться в раскрывающемся списке. Это связано с тем, что виртуальные сети на основе ARM не поддерживаются доменными службами Azure AD.

9. Убедитесь, что выбранное имя домена DNS для управляемого домена не существует в виртуальной сети. Ситуация, когда имя уже существует, может быть вызвана следующими причинами.

   - Если в виртуальной сети уже существует домен с тем же DNS-именем домена.

   - Если для выбранной виртуальной сети установлено VPN-подключение к локальной сети, а у вас в локальной сети есть домен с тем же DNS-именем домена.

   - Если в виртуальной сети существует облачная служба с таким же именем.

10. После выбора параметров нажмите кнопку **Сохранить** в области задач в нижней части страницы, чтобы включить доменные службы Azure AD.

11. Во время включения доменных служб AD для вашего каталога на странице будет отображаться состояние "Ожидание...".

    ![Включение доменных служб — состояние ожидания](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Доменные службы Azure AD обеспечивают высокий уровень доступности для управляемого домена. При первом включении доменных служб Azure AD для домена вы заметите, что IP-адреса, по которым службы домена доступны в виртуальной сети, отображаются один за другим. Второй IP-адрес будет показан через некоторое время, когда служба включит высокий уровень доступности для вашего домена. Когда высокий уровень доступности настроен и активен для вашего домена, в разделе **Доменные службы** на вкладке **Настройка** будут отображаться два IP-адреса.

12. Через 20–30 минут вы увидите первый из этих IP-адресов в поле **IP-адрес** на странице **Настройка**.

    ![Доменные службы включены — подготовлен первый IP-адрес](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. Когда высокий уровень доступности для вашего домена будет активен, вы увидите на этой странице два IP-адреса. Это IP-адреса, по которым доменные службы Azure AD будут доступны в выбранной виртуальной сети. Запишите эти IP-адреса, чтобы обновить параметры DNS для виртуальной сети. Этот шаг позволяет виртуальным машинам в виртуальной сети подключиться к домену для таких операций, как присоединение к домену.

    ![Доменные службы включены — подготовлен оба IP-адреса](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] В зависимости от размера каталога Azure AD (число пользователей, групп и т. д.) на то, чтобы содержимое каталога стало доступным в доменных службах Azure AD, может потребоваться некоторое время. Этот процесс синхронизации происходит в фоновом режиме. Для больших каталогов с десятками тысяч объектов на синхронизацию всех пользователей, групп и учетных данных может уйти один-два дня.

<br>

## Задача 4. Обновление настроек DNS для виртуальной сети Azure
Следующая задача — [обновление параметров DNS для виртуальной сети Azure](active-directory-ds-getting-started-dns.md).

<!---HONumber=AcomDC_0706_2016-->
<properties 
    pageTitle="Учебник. Интеграция Azure Active Directory с PagerDuty | Microsoft Azure" 
    description="Узнайте, как использовать PagerDuty с Azure Active Directory для реализации единого входа, автоматической подготовки пользователей и выполнения других задач." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/08/2016" 
    ms.author="jeedes" />

#Учебник. Интеграция Azure Active Directory с PagerDuty
  
Цель данного учебника — показать интеграцию Azure и PagerDuty. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

-   Действующая подписка на Azure
-   Клиент PagerDuty.
  
По завершении работы с этим руководством пользователи Azure AD, назначенные в PagerDuty, будут иметь возможность единого входа в приложение на веб-сайте PagerDuty компании (вход, инициированный поставщиком услуг) или следуя указаниям в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).
  
Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1.  Включение интеграции приложений для PagerDuty
2.  Настройка единого входа
3.  Настройка подготовки учетных записей пользователей
4.  Назначение пользователей

![Сценарий](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Сценарий")
##Включение интеграции приложений для PagerDuty
  
В этом разделе показано, как включить интеграцию приложений для PagerDuty.

###Чтобы включить интеграцию приложений для PagerDuty, выполните следующие действия:

1.  На портале управления Azure в левой области навигации нажмите **Active Directory**.

    ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")

2.  Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.  Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Приложения")

4.  В нижней части страницы нажмите кнопку **Добавить**.

    ![Добавление приложения](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Добавление приложения")

5.  В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

    ![Добавить приложение из коллекции](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Добавить приложение из коллекции")

6.  В **поле поиска** введите **Pagerduty**.

    ![Коллекция приложений](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Коллекция приложений")

7.  В области результатов выберите **Pagerduty** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##Настройка единого входа
  
В этом разделе показано, как разрешить пользователям проходить аутентификацию в PagerDuty со своей учетной записью Azure AD, используя федерацию на основе протокола SAML. В рамках этой процедуры потребуется создать файл сертификата в кодировке Base-64. Если вы не знакомы с этой процедурой, посмотрите видео [Как преобразовать двоичный сертификат в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

###Чтобы настроить единый вход, выполните следующие действия.

1.  На странице интеграции с приложением **PagerDuty** классического портала Azure щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "Настройка единого входа")

2.  На странице **Как пользователи должны входить в PagerDuty** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "Настройка единого входа")

3.  На странице **Настройка URL-адреса приложения** в текстовом поле **URL-адрес входа в PagerDuty** введите свой URL-адрес, используя следующий шаблон *https://\<имя-клиента>.Pagerduty.com*, а затем нажмите кнопку **Далее**.

    ![Настройка URL-адреса приложения](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Настройка URL-адреса приложения")

4.  На странице **Настройка единого входа в PagerDuty** нажмите кнопку **Загрузить сертификат**, а затем сохраните файл сертификата на свой компьютер.

    ![Настройка единого входа](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "Настройка единого входа")

5.  В другом окне браузера войдите на свой сайт PagerDuty компании в качестве администратора.

6.  В меню в верхней части страницы щелкните **Параметры учетной записи**.

    ![Параметры учетной записи](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Параметры учетной записи")

7.  Щелкните **Единый вход**.

    ![Единый вход](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "Единый вход")

8.  На странице «Включение единого входа» выполните следующие действия:

    ![Включение единого входа](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Включение единого входа")

    1.  Создайте файл **в кодировке Base-64** из скачанного сертификата.

        >[AZURE.TIP] Дополнительные сведения можно узнать в видео [Преобразование двоичного сертификата в текстовый файл](http://youtu.be/PlgrzUZ-Y1o).

    2.  Откройте сертификат в кодировке Base-64 в блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте его в текстовое поле **Сертификат X.509**.
    3.  На странице **Настройка единого входа в PagerDuty** классического портала Azure скопируйте значение поля **URL-адрес удаленного входа** и вставьте его в текстовое поле **URL-адрес входа**.
    4.  На странице **Настройка единого входа в PagerDuty** классического портала Azure скопируйте значение поля **URL-адрес удаленного выхода** и вставьте его в текстовое поле **URL-адрес выхода**.
    5.  Установите флажок **Включить единый вход**.
    6.  Нажмите кнопку **Сохранить изменения**.

9.  На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "Настройка единого входа")
##Настройка подготовки учетных записей пользователей
  
Чтобы пользователи Azure AD могли выполнять вход в PagerDuty, они должны быть подготовлены для PagerDuty. В случае с PagerDuty подготовка выполняется вручную.

###Чтобы подготовить учетные записи пользователей, выполните следующие действия:

1.  Выполните вход в клиент **PagerDuty**.

2.  В меню в верхней части страницы щелкните **Пользователи**.

3.  Щелкните **Добавить пользователей**.

    ![Добавление пользователей](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Добавление пользователей")

4.  В диалоговом окне **Приглашение команды** заполните текстовые поля **Имя и фамилия** и **Электронный адрес** данными пользователя Azure AD, которого необходимо подготовить, щелкните **Добавить** и нажмите кнопку **Отправить приглашения**.

    ![Приглашение команды](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Приглашение команды")

    >[AZURE.NOTE] Все добавленные пользователи получат приглашение создать учетную запись PagerDuty.

>[AZURE.NOTE] Вы можете использовать любые другие инструменты создания учетных записей пользователя PagerDuty или API, предоставляемые PagerDuty для подготовки учетных записей пользователя AAD.

##Назначение пользователей
  
Чтобы проверить свою конфигурацию, предоставьте пользователям Azure AD, которые должны использовать приложение, доступ путем их назначения.

###Чтобы назначить пользователей PagerDuty, выполните следующие действия:

1.  На классическом портале Azure создайте тестовую учетную запись.

2.  На странице интеграции с приложением **PagerDuty** щелкните **Назначить пользователей**.

    ![Назначение пользователей](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Назначить пользователей")

3.  Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.

    ![Да](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Да")
  
Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->
<properties 
    pageTitle="Учебник. Интеграция Azure Active Directory с SimpleNexus | Microsoft Azure" 
    description="Узнайте, как использовать SimpleNexus вместе с Azure Active Directory для реализации единого входа, автоматической подготовки к работе и многого другого." 
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
    ms.date="06/29/2016" 
    ms.author="jeedes" />

#Учебник. Интеграция Azure Active Directory с SimpleNexus
  
Цель данного учебника — показать интеграцию Azure и SimpleNexus. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

-   Действующая подписка на Azure
-   Подписка с поддержкой единого входа SimpleNexus.
  
После завершения этого руководства пользователи Azure AD, назначенные SimpleNexus, будут иметь возможность единого входа в приложение на веб-сайте компании SimpleNexus (вход, инициированный поставщиком услуг) или с помощью инструкций из статьи [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).
  
Сценарий, описанный в этом учебнике, состоит из следующих блоков:

1.  Включение интеграции приложений для SimpleNexus
2.  Настройка единого входа
3.  Настройка подготовки учетных записей пользователей
4.  Назначение пользователей

![Сценарий](./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Сценарий")
##Включение интеграции приложений для SimpleNexus
  
В этом разделе показано, как включить интеграцию приложений для SimpleNexus.

###Чтобы включить интеграцию приложений для SimpleNexus, выполните следующие действия:

1.  На классическом портале Azure в области навигации слева щелкните **Active Directory**.

    ![Active Directory](./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2.  Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3.  Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

    ![Приложения](./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Приложения")

4.  В нижней части страницы нажмите кнопку **Добавить**.

    ![Добавление приложения](./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Добавление приложения")

5.  В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

    ![Добавить приложение из коллекции](./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Добавить приложение из коллекции")

6.  В **поле поиска** введите **SimpleNexus**.

    ![Коллекция приложений](./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Коллекция приложений")

7.  В области результатов выберите **SimpleNexus** и нажмите кнопку **Завершить**, чтобы добавить приложение.

    ![SimpleNexus](./media/active-directory-saas-simplenexus-tutorial/IC809578.png "SimpleNexus")
##Настройка единого входа
  
В этом разделе показано, как разрешить пользователям проходить аутентификацию в SimpleNexus со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

###Чтобы настроить единый вход, выполните следующие действия.

1.  На классическом портале Azure на странице интеграции с приложением **SimpleNexus** щелкните **Настроить единый вход**, чтобы открыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Настройка единого входа")

2.  На странице **Как пользователи должны входить в SimpleNexus?** выберите **Единый вход Microsoft Azure AD** и нажмите кнопку **Далее**.

    ![Настройка единого входа](./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Настройка единого входа")

3.  На странице **Настройка URL-адреса приложения** введите свой URL-адрес в текстовое поле **URL-адрес для входа в SimpleNexus**, используя шаблон "*https://simplenexus.com/CompanyName\_login*", а затем нажмите кнопку **Далее**.

    ![Настройка URL-адреса приложения](./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Настройка URL-адреса приложения")

4.  На странице **Настройка единого входа в SimpleNexus** нажмите кнопку **Загрузить метаданные**, а затем перешлите файл метаданных в группу поддержки SimpleNexus.

    ![Настройка единого входа](./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Настройка единого входа")

    >[AZURE.NOTE] Единый вход должна включить группа поддержки SimpleNexus.

5.  На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**.

    ![Настройка единого входа](./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Настройка единого входа")
##Настройка подготовки учетных записей пользователей
  
Чтобы пользователи Azure AD могли выполнять вход в SimpleNexus, они должны быть подготовлены для работы с SimpleNexus. В случае SimpleNexus подготовка выполняется вручную администратором клиента.

>[AZURE.NOTE] Вы можете использовать любые другие инструменты создания учетных записей пользователей SimpleNexus или API, предоставляемые SimpleNexus для подготовки учетных записей пользователей AAD.

##Назначение пользователей
  
Чтобы проверить свою конфигурацию, предоставьте доступ пользователям Azure AD, которым нужно разрешить работу с приложением, назначив их.

###Чтобы назначить пользователей SimpleNexus, выполните следующие действия:

1.  На классическом портале Azure создайте тестовую учетную запись.

2.  На странице интеграции с приложением **SimpleNexus** нажмите кнопку **Назначить пользователей**.

    ![Назначить пользователей](./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Назначить пользователей")

3.  Выберите тестового пользователя, нажмите кнопку **Назначить**, а затем — **Да**, чтобы подтвердить назначение.

    ![Да](./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Да")
  
Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0629_2016-->
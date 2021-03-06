<properties 
   pageTitle="Управление учетной записью хранения StorSimple | Microsoft Azure"
   description="Здесь объясняется, как можно использовать страницу ";Настройка"; в диспетчере StorSimple для добавления, изменения и удаления или смены ключей безопасности для учетной записи хранения."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/29/2016"
   ms.author="v-sharos" />

# Использование службы диспетчера StorSimple для управления учетной записью хранения

## Обзор

На странице **Настройка** представлены все параметры глобальной службы, создаваемые в службе диспетчера StorSimple. Эти параметры могут применяться для всех устройств, подключенных к службе, включая:

- учетные записи хранения; 
- шаблоны пропускной способности; 
- записи системы контроля доступа. 

В этом учебнике объясняется, как с помощью страницы **Настройка** добавлять, изменять или удалять учетные записи хранения или менять для них ключи безопасности.

 ![Страница "Настройка"](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)

Учетные записи хранения содержат учетные данные, используемые устройством для доступа к учетной записи хранения у поставщика облачной службы. Для учетных записей хранения Microsoft Azure это такие учетные данные, как имя учетной записи и основной ключ доступа.

На странице **Настройка** все учетные записи хранения, созданные для подписки выставления счетов, отображаются в виде таблицы, содержащей следующие сведения:

- **Имя** — уникальное имя, назначенное учетной записи при ее создании.
- **SSL включен** — включение протокола SSL, чтобы обмен данными между облаком и устройством осуществлялся по безопасному каналу.
- **Используется** — число томов, использующих учетную запись хранения.

Вот наиболее типичные задачи, которые можно выполнить на странице **Настройка** для учетных записей хранения:

- добавление учетной записи хранения; 
- изменение учетной записи хранения; 
- Удаление учетной записи хранения 
- смена ключей учетных записей хранения. 

## Типы учетных записей хранения

Существует три типа учетных записей хранения, которые можно использовать на устройстве StorSimple.

- **Автоматически созданные учетные записи хранения** — как ясно из названия, этот тип учетной записи хранения создается автоматически при создании службы. Дополнительные сведения о создании этой учетной записи хранения см. в подразделе [Шаг 1. Создание новой службы](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) раздела [Развертывание локального устройства StorSimple](storsimple-deployment-walkthrough.md). 
- **Учетные записи хранения в подписке службы** — это учетные записи хранилища Azure, которые связаны с подпиской той же службы. Дополнительные сведения о том, как создаются эти учетные записи хранения, см. в разделе [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md). 
- **Учетные записи хранения вне подписки на службу** — это учетные записи хранения Azure, которые не связаны со службой и скорее всего существовали до создания службы.

## Добавление учетной записи хранения

Вы можете добавить учетную запись хранения, указав уникальное понятное имя и учетные данные для доступа, связанные с учетной записью хранения (у указанного поставщика облачной службы). Также можно включить режим SSL для создания безопасного сетевого канала связи между устройством и облаком.

Для одного поставщика облачных служб можно создать несколько учетных записей. Следует, однако, иметь в виду, что после создания учетной записи хранения поставщика облачных служб изменить нельзя.

При сохранении учетной записи хранения служба пытается связаться с поставщиком облачной службы. В этот момент выполняется аутентификация по учетным данным и предоставленным данным для доступа. Учетная запись хранения создается только в том случае, если аутентификация завершается успешно. При сбое аутентификации, будет отображаться соответствующее сообщение об ошибке.

Учетные записи хранения Resource Manager, созданные на портале Azure, также поддерживаются StorSimple. Учетные записи хранения Resource Manager не будут отображаться в раскрывающемся списке для выбора при попытке создать контейнер томов; в нем будут доступны только учетные записи хранения, созданные на классическом портале Azure. Чтобы добавить учетные записи хранения Resource Manager, нужно добавить учетную запись хранения, как описано ниже.

> [AZURE.NOTE] Процедура добавления учетной записи хранения зависит от используемой версии программного обеспечения StorSimple. Убедитесь, что процедуры для вашей версии StorSimple выполняются правильно.


[AZURE.INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## изменение учетной записи хранения;

Учетную запись хранения, используемую контейнером тома, можно изменить. При изменении учетной записи хранения, которая используется в настоящий момент, единственным полем, доступным для изменения, является ключ доступа учетной записи хранения. Можно предоставить новый ключ доступа к хранилищу и сохранить обновленные параметры.

#### Изменение учетной записи хранения

1. На главной странице служб выберите службу, дважды щелкните имя службы и выберите элемент **Настройка**.

2. Щелкните **Добавить/изменить учетную запись хранения**.

3. В диалоговом окне **Добавление или изменение учетных записей хранения**:

  1. В раскрывающемся списке **Учетные записи хранения** выберите существующую учетную запись, которую нужно изменить. Это также могут быть учетные записи хранения, которые были автоматически созданы при создании службы.
  2. При необходимости можно изменить параметр **Включить режим SSL**.
  3. Ключи доступа учетной записи хранения можно сменить. Дополнительные сведения о смене ключей см. в разделе [Смена ключей учетных записей хранения](#key-rotation-of-storage-accounts).
  4. Щелкните значок с изображением флажка ![значок галочки](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png), чтобы сохранить настройки. Параметры на странице **Настройки** обновятся. Щелкните **Сохранить**, чтобы сохранить обновленные параметры.

     ![Изменение учетной записи хранения](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)
  
## Удаление учетной записи хранения

> [AZURE.IMPORTANT] Учетную запись хранения можно удалить только в том случае, если она не используется контейнером томов. Если учетная запись хранения используется контейнером томов, сначала следует удалить контейнер томов, а затем удалить соответствующую учетную запись хранения.

#### Удаление учетной записи хранения

1. На главной странице службы диспетчера StorSimple выберите службу, дважды щелкните ее имя и щелкните **Настроить**.

2. В таблице учетных записей хранения наведите указатель мыши на учетную запись, которую требуется удалить.

3. Значок удаления (**x**) для этой учетной записи хранения будет отображаться в крайнем правом столбце. Щелкните значок **x**, чтобы удалить учетные данные.

4. При появлении запроса на подтверждение нажмите кнопку **Да**, чтобы продолжить удаление. Таблица будет обновляться с учетом изменений.

## Смена ключей учетных записей хранения

По соображениям безопасности смена ключей в центрах обработки данных часто является обязательным требованием.

> [AZURE.NOTE] Информация о смене ключей и процедура замены применима только к учетным записям хранения Microsoft Azure. Если вы используете другого поставщика облачных служб, управлять ключами учетной записи хранения можно через панель мониторинга этого поставщика.
 
Для каждой подписки Microsoft Azure можно иметь одну или несколько связанных учетных записей хранения. Управление доступом к этим учетным записям осуществляется с помощью ключей подписки и ключей доступа для каждой учетной записи хранения.

При создании учетной записи хранения Microsoft Azure создает два 512-разрядных ключа доступа к хранилищу, которые используются для проверки подлинности при доступе к учетной записи хранения. Два ключа доступа к хранилищу позволяют повторно создавать ключи без прерывания работы службы хранилища или доступа к этой службе. Ключ, который используется в настоящий момент, называется *первичным* ключом, а его резервная копия— *вторичным* ключом. Один из этих двух ключей указывается при обращении устройства Microsoft Azure StorSimple к вашему поставщику услуг облачного хранилища.

## Что такое смена ключей?

Как правило, приложения для доступа к данным используют только один из ключей. Через определенный период времени можно переключиться на использование второго ключа. После переключения приложений на вторичный ключ можно прекратить использование первого ключа и создать новый ключ. Два ключа позволяют приложениям получать доступ к данным без простоев.

Ключи учетной записи хранения всегда хранятся в службе в зашифрованном виде. Их можно сбросить с помощью службы диспетчера StorSimple. Служба может получить первичный и вторичный ключи для всех учетных записей хранения в одной подписке, включая учетные записи, созданные в службе хранилища, а также учетные записи хранения по умолчанию, созданные при первом создании службы диспетчера StorSimple. Служба диспетчера StorSimple будет всегда получать эти ключи с классического портала Azure и хранить их в зашифрованном виде.

## Рабочий процесс смены ключей

Администратор Microsoft Azure может повторно создавать и изменять первичный и вторичный ключи путем прямого доступа к учетной записи хранения (с помощью службы хранилища Microsoft Azure). Служба диспетчера StorSimple не выявляет эти изменения автоматически.

Чтобы сообщить службе диспетчера StorSimple об изменениях, необходимо получить доступ к службе диспетчера StorSimple, затем перейти к учетной записи хранения, после чего синхронизировать основной или дополнительный ключ (в зависимости от того, какой из них был изменен). Затем служба возвращает последний ключ, шифрует ключи и передает зашифрованный ключ на устройство.

#### Синхронизация ключей для учетных записей хранения в той же подписке, что и служба (только Azure)

1. На странице **Службы** выберите вкладку **Настройка**.

2. Щелкните **Добавить/изменить учетную запись хранения**.

3. В диалоговом окне сделайте следующее.

  1. Выберите учетную запись хранения с ключом, который необходимо синхронизировать. Ключи учетной записи хранения шифруются при отображении.
  2. В службе диспетчера StorSimple необходимо обновить ключ, который ранее был изменен в службе хранилища Microsoft Azure. Если был изменен (создан повторно) основной ключ доступа, щелкните **Синхронизировать основной ключ**. Если был изменен дополнительный ключ, щелкните **Синхронизировать дополнительный ключ**.

    ![Синхронизация ключей](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### Синхронизация ключей для учетных записей хранения вне подписки на службу

1. На странице **Службы** выберите вкладку **Настройка**.

2. Щелкните **Добавить/изменить учетную запись хранения**.

3. В диалоговом окне сделайте следующее.

  1. Выберите учетную запись хранения с ключом доступа, который необходимо обновить.
  2. Обновлять ключ доступа к хранилищу необходимо в службе диспетчера StorSimple. В этом случае его можно увидеть. Введите новый ключ в поле **Ключ доступа к учетной записи хранения**. 
  3. Сохраните изменения. Ключ доступа к учетной записи хранения теперь должен быть обновлен.

## Дальнейшие действия

- [Узнайте о больше о безопасности StorSimple](storsimple-security.md).
- Узнайте больше об [использовании службы диспетчера StorSimple для администрирования устройства StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0518_2016-->
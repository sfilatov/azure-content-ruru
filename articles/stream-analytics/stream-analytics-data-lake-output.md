<properties
	pageTitle="Выходные данные хранилища озера данных в Stream Analytics | Microsoft Azure"
	description="Настройка проверки подлинности и авторизации хранилища озера данных в задании Stream Analytics"
	keywords=""
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="08/30/2016"
	ms.author="jeffstok"
/>

# Выходные данные хранилища озера данных в Stream Analytics

Задания Stream Analytics поддерживают несколько методов вывода, одним из которых является [хранилище озера данных Azure](https://azure.microsoft.com/services/data-lake-store/). Хранилище озера данных Azure — это крупномасштабный репозиторий корпоративного уровня для рабочих нагрузок анализа больших данных. Озеро данных Azure позволяет сохранять данные с любым размером, типом и скоростью приема в одном месте для эксплуатационной и исследовательской аналитики.

## Авторизация учетной записи хранения озера данных Azure

1.  Если на портале управления Azure для вывода данных выбрано хранилище озера данных, вам будет предложено разрешить использование существующего хранилища озера данных или запросить доступ к предварительной версии хранилища озера данных на классическом портале Azure.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)

2.  Если у вас уже есть доступ к хранилищу озера данных, нажмите кнопку "Авторизовать сейчас", после чего на непродолжительное время откроется страница с сообщением "Перенаправление для авторизации...". Страница закроется автоматически, а затем появится страница для настройки выходных данных хранилища озера данных.

Если вы не зарегистрировались для использования предварительной версии Data Lake Store, перейдите по ссылке "Зарегистрироваться", чтобы отправить запрос, или следуйте [инструкциям по началу работы](../data-lake-store/data-lake-store-get-started-portal.md).

## Настройка свойств выходных данных хранилища озера данных

После проверки подлинности учетной записи хранилища озера данных можно настроить свойства для выходных данных хранилища озера данных. В таблице ниже приведены имена и описание свойств для настройки выходных данных хранилища озера данных.

<table>
<tbody>
<tr>
<td><B>ИМЯ СВОЙСТВА</B></td>
<td><B>ОПИСАНИЕ</B></td>
</tr>
<tr>
<td>Псевдоним выходных данных</td>
<td>Это понятное имя, которое используется в запросах для направления выходных данных запроса в соответствующее хранилище озера данных.</td>
</tr>
<tr>
<td>Учетная запись хранилища озера данных</td>
<td>Имя учетной записи хранения, в которую отправляются выходные данные. Появится раскрывающийся список учетных записей хранилища озера данных, к которым имеет доступ вошедший на портал пользователь.</td>
</tr>
<tr>
<td>Шаблон префикса пути (<I>необязательное свойство</I>)</td>
<td>Путь к файлу, используемый для записи файлов в указанной учетной записи хранилища озера данных. <BR>{date}, {time}<BR>Пример 1. folder1/logs/{дата}/{время}<BR>Пример 2. folder1/logs/{дата}</td>
</tr>
<tr>
<td>Формат даты (<I>необязательное свойство</I>)</td>
<td>Если в префиксе пути используется маркер даты, вы можете выбрать формат даты для упорядочивания своих файлов. Пример: ГГГГ/ММ/ДД</td>
</tr>
<tr>
<td>Формат времени (<I>необязательное свойство</I>)</td>
<td>Если в префиксе пути используется маркер времени, укажите формат времени для упорядочивания своих файлов. В настоящее время поддерживается только один формат&#160;— ЧЧ.</td>
</tr>
<tr>
<td>Формат сериализации событий</td>
<td>Формат сериализации для выходных данных. Поддерживаются форматы JSON, CSV и Avro.</td>
</tr>
<tr>
<td>Кодирование</td>
<td>Если используется формат CSV или JSON, необходимо указать формат кодирования. В настоящее время единственным поддерживаемым форматом кодирования является UTF-8.</td>
</tr>
<tr>
<td>Разделитель</td>
<td>Применяется только для сериализации CSV-файлов. Служба Stream Analytics позволяет использовать ряд распространенных разделителей для сериализации данных в формате CSV. Поддерживаются такие разделители: запятая, точка с запятой, пробел, табуляция и вертикальная черта.</td>
</tr>
<tr>
<td>Формат</td>
<td>Применяется только для сериализации JSON. Вариант «строки-разделители» предусматривает форматирование выходных данных таким образом, что каждый объект JSON будет отделен новой строкой. Вариант «массив» означает, что выходные данные будут отформатированы как массив объектов JSON.</td>
</tr>
</tbody>
</table>

## Обновление авторизации хранилища озера данных

Сейчас существует ограничение, при котором маркер проверки подлинности необходимо обновлять вручную каждые 90 дней для всех заданий с выходными данными хранилища озера данных. Кроме того, необходимо будет повторно выполнить проверку подлинности учетной записи хранения озера данных, если с момента создания задания или последней проверки подлинности был изменен пароль. Признаком этой проблемы является отсутствие выходных данных задания и наличие ошибки в журналах операций, означающей необходимость повторной авторизации.

Чтобы устранить эту проблему, остановите выполнение задания и перейдите к выходным данным хранилища озера данных. Щелкните ссылку "Обновить авторизацию", после чего на непродолжительное время откроется страница с сообщением "Перенаправление для авторизации...". Страница автоматически закроется, и в случае успешного выполнения появится сообщение "Авторизация успешно возобновлена". В нижней части страницы нажмите кнопку "Сохранить", а затем перезапустите задание с момента последней остановки, чтобы избежать потери данных.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)

<!---HONumber=AcomDC_0831_2016-->
<properties
	pageTitle="Создание пользовательской панели мониторинга в Log Analytics | Microsoft Azure"
	description="Это руководство поможет вам понять, как панели мониторинга в службе Log Analytics помогают следить за состоянием вашей среды с помощью визуализации всех ваших сохраненных запросов поиска по журналам."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Создание пользовательской панели мониторинга в Log Analytics

Это руководство поможет вам понять, как панели мониторинга в службе Log Analytics помогают следить за состоянием вашей среды с помощью визуализации всех ваших сохраненных запросов поиска по журналам.

![Пример панели мониторинга](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

## Создание панели мониторинга

Для начала перейдите в службе OMS на страницу обзора. Для этого в области навигации слева нажмите кнопку Overview (Обзор). Слева вы увидите плитку My Dashboard (Моя панель мониторинга). Щелкните ее, чтобы открыть свою панель мониторинга.

![Обзор](./media/log-analytics-dashboards/oms-dashboards-overview.png)



## Добавление плитки

В разделе панелей мониторинга сведения на плитках берутся из сохраненных запросов поиска журналов. В службе OMS есть много уже готовых запросов поиска журналов, поэтому вы сможете сразу приступить к созданию панели мониторинга. Пошаговые инструкции представлены в виде рисунков.

![Инструкция в виде рисунков](./media/log-analytics-dashboards/oms-dashboards-pictorial.png)

В представлении My Dashboard (Моя панель мониторинга) в нижней части страницы просто щелкните значок шестеренки, чтобы перейти в режим настройки. Справа на странице откроется панель со всеми вашими сохраненными запросами поиска журналов в рабочей области.

![Добавление плиток 1](./media/log-analytics-dashboards/oms-dashboards-add-tile1.png)

Чтобы представить сохраненный запрос поиска журнала в виде плитки, просто перетащите его на пустое место слева. При перетаскивании запрос превращается в плитку.

![Добавление плиток 2](./media/log-analytics-dashboards/oms-dashboards-add-tile2.png)

![Добавление плиток 3](./media/log-analytics-dashboards/oms-dashboards-add-tile3.png)


## Изменение плитки

В представлении My Dashboard (Моя панель мониторинга) в нижней части страницы просто щелкните значок шестеренки, чтобы перейти в режим настройки. Щелкните плитку, которую нужно изменить. На панели справа появятся параметры, которые можно изменять.

![Изменение плитки](./media/log-analytics-dashboards/oms-dashboards-edit-tile.png)

### Визуализация плиток#
Для плиток предложено два варианта визуализации.

|тип диаграммы|назначение|
|---|---|
|![Линейчатая диаграмма](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png)|На ней показывается временная шкала результатов сохраненного запроса поиска журнала с сортировкой по полям (зависит от того, сортирует ли поиск журнала найденные результаты по полям).
|![метрика](./media/log-analytics-dashboards/oms-dashboards-metric.png)|На ней показывается число, которое означает, сколько раз был найден элемент по запросу поиска журнала. Плитки с метрикой позволяют задать пороговое значение, по достижении которого плитка будет выделяться.|

### Пороговое значение
Чтобы задать для плитки пороговое значение, используйте визуализацию в виде метрики. Установите для параметра Threshold (Пороговое значение) значение On (Включено). Укажите, следует ли выделять плитку, когда достигнуто пороговое значение, и в поле ниже задайте требуемое значение.

## Упорядочение панели мониторинга
Чтобы упорядочить панель мониторинга, откройте представление My Dashboard (Моя панель мониторинга) и в нижней части страницы щелкните значок шестеренки. Активируется режим настройки. Выберите и перетащите плитку в нужное место.

![Упорядочение панели мониторинга](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## Удаление плитки
Чтобы удалить плитку, откройте представление My Dashboard (Моя панель мониторинга) и в нижней части страницы щелкните значок шестеренки. Активируется режим **настройки**. Выделите плитку, которую требуется удалить, а затем на панели справа нажмите кнопку **Remove Tile** (Удалить плитку).

![Удаление плитки](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## Дальнейшие действия

- Создайте оповещения в службе Log Analytics для создания уведомлений и устранения проблем.

<!---HONumber=AcomDC_0504_2016-->
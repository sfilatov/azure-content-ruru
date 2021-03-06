<properties
   pageTitle="Создание пользовательской пробы для шлюза приложений с помощью портала | Microsoft Azure"
   description="Узнайте, как создать пользовательскую пробу для шлюза приложений с помощью портала."
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace" />

# Создание пользовательской пробы для шлюза приложений с помощью портала

> [AZURE.SELECTOR]
- [Портал Azure](application-gateway-create-probe-portal.md)
- [PowerShell и диспетчер ресурсов Azure](application-gateway-create-probe-ps.md)
- [Классическая модель — Azure PowerShell](application-gateway-create-probe-classic-ps.md)

.<BR>

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## Сценарий

В следующем сценарии рассматривается создание пользовательской пробы работоспособности в существующем шлюзе приложений. Предполагается, что действия по [созданию шлюза приложений](application-gateway-create-gateway-portal.md) уже выполнены.

## <a name="createprobe"></a>Создание пробы

На портале пробы настраиваются в два этапа. Первым шагом является создание пробы, затем следует добавить ее в параметры HTTP серверной части приложения шлюза.

### Шаг 1

Перейдите по адресу http://portal.azure.com и выберите имеющийся шлюз приложений.

.![Обзор шлюза приложений][1]

### Шаг 2

Щелкните **Пробы** и нажмите кнопку **Добавить**, чтобы добавить новую пробу.

.![Колонка добавления пробы с заполненной информацией][2]

### Шаг 3.

Введите необходимые сведения о пробе, затем нажмите кнопку **ОК**.

- **Имя** — понятное имя пробы, которое отображается на портале.
- **Узел** — имя узла, который используется для пробы.
- **Путь** — остальная часть полного URL-адреса пользовательской пробы.
- **Интервал (с)** — интервал между выполнением проб работоспособности.
- **Время ожидания (с)** — время ожидания пробы.
- **Порог состояния неработоспособности** — число неудачных попыток, после которых состояние считается неработоспособным.

> [AZURE.IMPORTANT] Имя узла не является именем сервера. Это имя виртуального узла, запущенного на сервере приложений. Проба отправляется по адресу http://(имя\_узла):(порт\_из\_параметров\_HTTP)/urlPath.

.![Параметры конфигурации пробы][3]

## Добавление пробы в шлюз

После создания пробы пора добавить ее в шлюз. Настройки пробы задаются в параметрах протокола HTTP серверной части приложения шлюза.

### Шаг 1

Щелкните **Параметры HTTP** для шлюза приложения и выберите текущие параметры HTTP серверной части в окне, чтобы открыть колонку конфигурации.

.![Окно параметров HTTPS][4]

### Шаг 2

В колонке параметров **appGatewayBackEndHttp** щелкните **Использовать пользовательский зонд** и выберите пробу, созданную в разделе [Создание пробы](#createprobe). По завершении нажмите кнопку **ОК**, и параметры будут применены.

.![Колонка параметров серверной части шлюза приложений][5]

Проба по умолчанию проверяет доступ по умолчанию к веб-приложению. После создания пользовательской пробы шлюз приложений использует определенный пользовательский путь для мониторинга работоспособности выбранной серверной части. На основе определенных критериев шлюз приложений проверяет файл, указанный в пробе. Если вызов по адресу <узел>:<порт>/<путь> не возвращает ответ состояния "Http 200 ОК", то по достижении порогового значения неработоспособности сервер выводится из ротации. Выполнение проб неработоспособного экземпляра продолжается, чтобы определить, когда она снова станет работоспособен. После добавления экземпляра обратно пул работоспособных серверов трафик снова начинает поступать к нему, и выполнение проб этого экземпляра продолжается с указанным пользователем интервалом в обычном режиме.


## Дальнейшие действия

В можете узнать, как [настроить разгрузку SSL](application-gateway-ssl-portal.md) с использованием шлюза приложений Azure.

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png

<!---HONumber=AcomDC_0810_2016-->
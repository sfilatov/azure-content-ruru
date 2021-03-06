<properties
	pageTitle="Устранение неполадок подключения к удаленному рабочему столу на виртуальной машине Azure | Microsoft Azure"
	description="Если не удается получить доступ к виртуальной машине Azure, можно ознакомиться с краткими инструкциями по устранению неполадок протокола RDP, справкой по сообщениям об ошибках и подробными инструкциями по устранению неполадок сети."
	keywords="Ошибка удаленного рабочего стола, ошибка подключения к удаленному рабочему столу, не удается подключиться к виртуальной машине, диагностика удаленного рабочего стола"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="09/01/2016"
	ms.author="iainfou"/>

# Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows

Подключение протокола удаленного рабочего стола (RDP) к виртуальной машине Azure под управлением Windows может завершиться неудачно по нескольким причинам. В этом случае доступ к виртуальной машине будет невозможен. Эта проблема может быть вызвана службой удаленного рабочего стола на виртуальной машине, сетевым подключением или клиентом удаленного рабочего стола на главном компьютере. В этой статье описываются некоторые из наиболее распространенных методов устранения проблем подключения по протоколу RDP. Если ваша проблема не описана в данной статье или вам по-прежнему не удается подключиться к виртуальной машине по протоколу RDP, можно прочитать [более подробные сведения об основных понятиях и этапах устранения неполадок RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md).

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.

<a id="quickfixrdp"></a>

## Действия по устранению неполадок
После выполнения каждого шага снова попробуйте подключиться к виртуальной машине.

1. Сбросьте удаленный доступ с помощью портала Azure или Azure PowerShell.
2. Перезапустите виртуальную машину.
3. Заново разверните виртуальную машину.
4. Проверьте правила групп безопасности сети или правила конечной точки облачных служб.
5. Изучите журналы консоли виртуальной машины с помощью портала Azure или Azure PowerShell.
6. Проверьте работоспособность ресурсов виртуальной машины на портале Azure.
7. Сбросьте пароль виртуальной машины.

Читайте статью дальше, если вам нужны более подробные инструкции или пояснения по работе с моделью развертывания с помощью Resource Manager и классической моделью развертывания.


<a id="fix-common-remote-desktop-errors"></a>
## Устранение неполадок в виртуальных машинах, созданных с использованием модели развертывания с помощью Resource Manager

После выполнения каждого шага устранения неполадок попробуйте подключиться к виртуальной машине еще раз.

> [AZURE.TIP] Если кнопка "Подключить" на портале неактивна и вы не используете канал [Express Route](../expressroute/expressroute-introduction.md) или [VPN-подключение типа "сеть — сеть"](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) для подключения к Azure, то прежде всего следует создать общедоступный IP-адрес и назначить его виртуальной машине. Только после этого вы сможете использовать протокол RDP. Дополнительные сведения см. в статье [IP-адреса в Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).

1. Сброс удаленного доступа с помощью Powershell.
	- [Установите и настройте последнюю версию Azure PowerShell](../powershell-install-configure.md), если у вас ее еще нет.

	- Сбросьте RDP-подключение с помощью любой из следующих команд PowerShell. Вместо `myRG`, `myVM`, `myVMAccessExtension` и расположения укажите значения, соответствующие параметрам вашей среды.

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
		-Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" `
		-Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" `
		-Location Westus
	```
	ИЛИ

  	```
	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" `
		-VMName "myVM" -Name "myVMAccess" -Location Westus
	```

	> [AZURE.NOTE] В приведенных выше примерах `myVMAccessExtension` или `MyVMAccess` — это имя нового расширения, которое будет установлено. Часто в качестве этого имени используется имя виртуальной машины. Если вы уже работали с агентом VMAccessAgent, то можете получить имя существующего расширения с помощью командлета `Get-AzureRmVM -ResourceGroupName "myRG" -Name "myVM"`, который возвращает свойства виртуальной машины. Имя расширения можно найти в разделе "Extensions" в выходных данных. Так как на виртуальной машине может существовать только один агент VMAccessAgent, при использовании `Set-AzureRmVMExtension` необходимо добавить параметр `-ForceReRun True`, чтобы повторно зарегистрировать агент.

2. Перезапустите виртуальную машину, чтобы перейти к другим проблемам запуска. Выберите **Обзор** > **Виртуальные машины** > *нужная виртуальная машина* > **Перезапустить**.

3. [Повторно разверните виртуальную машину на новом узле Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Обратите внимание, что после этой операции будут потеряны данные на временном диске, а также изменятся динамические IP-адреса, связанные с виртуальной машиной.
	
4. Убедитесь, что ваши [правила группы безопасности сети](../virtual-network/virtual-networks-nsg.md) разрешают прохождение трафика для протокола RDP (TCP-порт 3389).

5. Проверьте журнал консоли виртуальной машины или снимок экрана, чтобы устранить проблемы с загрузкой. Последовательно выберите **Обзор** > **Виртуальные машины** > *нужная виртуальная машина Windows* > **Поддержка и устранение неполадок** > **Диагностика загрузки**.

6. [Сбросьте пароль виртуальной машины](virtual-machines-windows-reset-rdp.md).

Если у вас по-прежнему возникают проблемы с протоколом RDP, можно [отправить запрос в службу поддержки](https://azure.microsoft.com/support/options/) или прочитать [более подробные сведения об основных понятиях и этапах устранения неполадок RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Устранение неполадок в виртуальных машинах, созданных с использованием классической модели развертывания

После выполнения каждого шага устранения неполадок попробуйте подключиться к виртуальной машине еще раз.

1. Выполните сброс конфигурации службы удаленных рабочих столов с помощью [портала Azure](https://portal.azure.com). Последовательно выберите **Обзор** > **Виртуальные машины (классика)** > *нужная виртуальная машина* > **Удаленный сброс**.

2. Перезапустите виртуальную машину, чтобы перейти к другим проблемам запуска. Последовательно выберите **Обзор** > **Виртуальные машины (классика)** > *нужная виртуальная машина* > **Перезапустить**.

3. [Повторно разверните виртуальную машину на новом узле Azure](virtual-machines-windows-redeploy-to-new-node.md).

	Обратите внимание, что после этой операции будут потеряны данные на временном диске, а также изменятся динамические IP-адреса, связанные с виртуальной машиной.
	
4. Убедитесь, что [конечная точка облачных служб пропускает трафик протокола RDP](../cloud-services/cloud-services-role-enable-remote-desktop.md).

5. Проверьте журнал консоли виртуальной машины или снимок экрана, чтобы устранить проблемы с загрузкой. Последовательно выберите **Обзор** > **Виртуальные машины (классика)** > *нужная виртуальная машина* > **Параметры** > **Диагностика загрузки**.

6. Проверьте работоспособность ресурсов виртуальной машины, чтобы выявить любые проблемы, связанные с платформой. Последовательно выберите **Обзор** > **Виртуальные машины (классика)** > *нужная виртуальная машина* > **Параметры** > **Проверить работоспособность**.

7. [Сбросьте пароль виртуальной машины](virtual-machines-windows-reset-rdp.md).

Если у вас по-прежнему возникают проблемы с протоколом RDP, можно [отправить запрос в службу поддержки](https://azure.microsoft.com/support/options/) или прочитать [более подробные сведения об основных понятиях и этапах устранения неполадок RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows

При попытке подключиться к виртуальной машине по протоколу RDP может появиться сообщение об определенной ошибке. Ниже перечислены наиболее распространенные сообщения об ошибках.

- [Удаленный сеанс отключен, так как отсутствуют доступные серверы лицензирования удаленных рабочих столов, которые могли бы провести лицензирование](#rdplicense).

- [Службе удаленного рабочего стола не удается найти имя компьютера](#rdpname).

- [Произошла ошибка проверки подлинности. Не удается связаться с локальным администратором безопасности»](#rdpauth).

- [Ошибка безопасности Windows: учетные данные не работают](#wincred).

- [Не удается подключиться к удаленному компьютеру](#rdpconnect).

<a id="rdplicense"></a>
### Удаленный сеанс отключен, поскольку отсутствуют доступные серверы лицензирования удаленных рабочих столов, которые могли бы провести лицензирование.

Причина: истек 120-дневный льготный период лицензирования для роли сервера удаленных рабочих столов, и вам необходимо установить лицензии.

В качестве решения вы можете сохранить локальную копию RDP-файла с портала, а затем установить подключение, выполнив следующую команду в командной строке PowerShell. В результате лицензирование будет отключено для данного подключения.

		mstsc <File name>.RDP /admin

Если вы не планируете устанавливать более двух одновременных подключений к удаленному рабочему столу виртуальной машины, вы можете удалить роль сервера удаленных рабочих столов с помощью диспетчера серверов.

Дополнительные сведения см. в записи блога [Azure VM fails with "No Remote Desktop License Servers available"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/) (Сбой виртуальной машины Azure с сообщением "отсутствуют доступные серверы лицензирования удаленных рабочих столов").

<a id="rdpname"></a>
### Службе удаленного рабочего стола не удается найти имя компьютера.

Причина: клиенту удаленного рабочего стола не удается разрешить имя компьютера в параметрах RDP-файла.

Возможные решения

- Если вы работаете в интрасети организации, убедитесь в том, что у вашего компьютера есть доступ к прокси-серверу и возможность передавать на него данные по протоколу HTTPS.

- Если вы работаете с локальным RDP-файлом, попробуйте воспользоваться файлом, созданным с помощью портала. Так можно гарантировать правильность DNS-имени виртуальной машины, имени облачной службы и номера порта конечной точки виртуальной машины. Вот пример RDP-файла, созданного порталом:

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

Адресная часть в этом RDP-файле состоит из:
- полного доменного имени облачной службы, в которой находится виртуальная машина (в этом примере — tailspin-azdatatier.cloudapp.net);

- внешнего TCP-порта конечной точки для обмена данными с удаленным рабочим столом (55919).

<a id="rdpauth"></a>
### Произошла ошибка проверки подлинности. Не удается связаться с локальным администратором безопасности».

Причина: целевой виртуальной машине не удается найти администратор безопасности, указанного в части имени пользователя ваших учетных данных.

Если имя пользователя имеет вид *система\_безопасности* \\ *имя\_пользователя* (например, CORP\\User1), то *система\_безопасности* в этой записи обозначает имя компьютера виртуальной машины (для локальной системы безопасности) или имя домена Active Directory.

Возможные решения

- Если учетная запись является локальной для виртуальной машины, проверьте правильность написания имени виртуальной машины.

- Если учетная запись относится к домену Active Directory, проверьте правильность написания имени домена.

- Если она является учетной записью домена Active Directory и имя домена указано правильно, убедитесь, что контроллер домена доступен для этого домена. В виртуальных сетях Azure с контроллерами домена этот компонент может быть часто недоступен, так как он не запущен. В качестве решения вместо учетной записи домена используйте учетную запись локального администратора.

<a id="wincred"></a>
### Ошибка безопасности Windows: учетные данные не работают.

Причина: целевой виртуальной машине не удается проверить имя и пароль учетной записи.

Компьютер Windows принимает данные локальных учетных записей и учетных записей домена.

- В случае локальных учетных записей используйте синтаксис *имя\_компьютера* \\ *имя\_пользователя* (например, SQL1\\Admin4798).
- В случае учетных записей домена используйте синтаксис *имя\_домена* \\ *имя\_пользователя* (например, CONTOSO\\peterodman).

Если вы повысили уровень виртуальной машины до контроллера домена в новом лесу Active Directory, локальная учетная запись администратора, под которой вы выполнили вход в систему, преобразуется в эквивалентную учетную запись с тем же паролем в новом лесу и домене. Затем локальная учетная запись удаляется.

Например, если вы вошли с локальной учетной записью DC1\\DCAdmin и повысили уровень виртуальной машины до контроллера домена corp.contoso.com в новом лесу, локальная учетная запись DC1\\DCAdmin удаляется, а вместо нее создается новая учетная запись домена CORP\\DCAdmin с тем же паролем.

Убедитесь, что имя учетной записи — это имя, которое виртуальная машина может проверить и принять как действительную учетную запись, а пароль указан правильно.

Указания по смене пароля учетной записи локального администратора см. в статье [Сброс пароля или службы удаленных рабочих столов для виртуальных машин Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
### Не удается подключиться к удаленному компьютеру.

Причина: у учетной записи, которую вы используете для подключения, нет прав для входа на удаленный рабочий стол.

На каждом компьютере Windows есть локальная группа пользователей удаленного рабочего стола. Она содержит учетные записи и группы, обладающие правом удаленного входа. Аналогичные права доступа есть и у участников локальной группы администраторов, хотя соответствующие учетные записи и не входят в локальную группу пользователей удаленного рабочего стола. На компьютерах, подключенных к домену, локальная группа администраторов также содержит учетные записи администраторов этого домена.

Убедитесь в том, что у учетной записи, которую вы используете для подключения, есть права для входа на удаленный рабочий стол. Для устранения этой проблемы воспользуйтесь учетной записью администратора домена или локального администратора, чтобы создать подключение к удаленному рабочему столу. Чтобы добавить требуемую учетную запись в локальную группу пользователей удаленного рабочего стола, используйте оснастку "Консоль управления (MMC)" (**Служебные программы > Локальные пользователи и группы > Группы > Пользователи удаленного рабочего стола**).

## Устранение общих неполадок удаленного рабочего стола

Если ни одна из указанных ошибок не возникла, но подключиться к виртуальной машине с помощью протокола удаленного рабочего стола не удается, прочтите [подробное руководство по устранению неполадок с удаленным рабочим столом](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## Дополнительные ресурсы

[Пакет диагностики Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Сброс пароля или службы удаленных рабочих столов для виртуальных машин Windows](virtual-machines-windows-reset-rdp.md)

[Установка и настройка Azure PowerShell](../powershell-install-configure.md)

[Устранение неполадок с подключением Secure Shell к виртуальной машине Azure под управлением Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Устранение неполадок доступа к приложению, выполняющемуся в виртуальной машине Azure](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0907_2016-->
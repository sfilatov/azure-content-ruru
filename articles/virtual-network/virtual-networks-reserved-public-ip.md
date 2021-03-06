<properties
   pageTitle="Зарезервированный IP-адрес | Microsoft Azure"
   description="Общие сведения о зарезервированных IP-адресах и о том, как ими управлять"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="jdial" />

# Обзор зарезервированных IP-адресов
IP-адреса в Azure делятся на две категории: динамические и зарезервированные. Общедоступные IP-адреса, управляемые Azure, являются динамическими по умолчанию. Это означает, что IP-адрес, используемый для заданной облачной службы (VIP) или для прямого доступа к виртуальной машине или экземпляру роли (ILPIP), время от времени может изменяться, при отключении или высвобождении ресурсов.

Чтобы предотвратить изменение IP-адресов, можно зарезервировать IP-адрес. Зарезервированные IP-адреса можно использовать только в качестве виртуального IP-адреса (VIP), гарантируя, что IP-адрес облачной службы будет оставаться таким же даже при отключении или высвобождении ресурсов. Кроме того, можно преобразовать существующие динамические IP-адреса, используемые в качестве виртуального IP-адреса, в зарезервированный IP-адрес.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Узнайте, как зарезервировать статический общедоступный IP-адрес, используя [модель развертывания с помощью Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Убедитесь, что вы понимаете как работают в Azure [IP-адреса](virtual-network-ip-addresses-overview-classic.md).

## Когда требуется зарезервированный IP-адрес?
- **Необходимо обеспечить резервирование IP-адреса в подписке**. Если требуется зарезервировать IP-адрес, который не будет освобожден из подписки ни при каких обстоятельствах, следует использовать зарезервированный общедоступный IP-адрес.
- **Необходимо сохранять IP-адрес облачной службы даже в случае остановленного или высвобожденного состояния (виртуальных машин)**. Если требуется доступ к службе по IP-адресу, который не изменится даже в том случае, если виртуальные машины в облачной службе будут остановлены или высвобождены.
- **Необходимо обеспечить предсказуемый IP-адрес для исходящего трафика из Azure**. Возможно, настройки локального брандмауэра разрешают трафик только с определенных IP-адресов. Резервирование IP-адреса позволяет знать исходный IP-адрес и избегать необходимости обновлять правила брандмауэра из-за его изменения.

## Часто задаваемые вопросы
1. Можно ли использовать зарезервированный IP-адрес для всех служб Azure?
  - Зарезервированные IP-адреса можно использовать только для виртуальных машин и экземпляров ролей облачных служб, представляемых через виртуальный IP-адрес.
1. Сколько зарезервированных IP-адресов можно установить?
  - На данный момент все подписки Azure допускают использование 20 зарезервированных IP-адресов. Однако вы можете запросить дополнительные зарезервированные IP-адреса. Дополнительные сведения см. на странице [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).
1. Взимается ли плата за зарезервированные IP-адреса?
  - Информацию о ценах см. в разделе [Сведения о ценах на зарезервированные IP-адреса](http://go.microsoft.com/fwlink/?LinkID=398482).
1. Как зарезервировать IP-адрес?
  - Для резервирования IP-адреса из определенного региона можно использовать PowerShell или интерфейс [REST API управления Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx). Этот зарезервированный IP-адрес связан с вашей подпиской. Зарезервировать IP-адрес с помощью портала управления нельзя.
1. Можно ли использовать эту возможность с виртуальными сетями на основе территориальной группы?
  - Зарезервированные IP-адреса поддерживаются только в региональных виртуальных сетях. Они не поддерживаются для виртуальных сетей, которые связаны с территориальными группами. Дополнительные сведения о связывании виртуальной сети с регионом или территориальной группой см. в разделе [О региональных виртуальных сетях и территориальных группах](virtual-networks-migrate-to-regional-vnet.md).

## Управление зарезервированными виртуальными IP-адресами

Прежде чем можно будет использовать зарезервированные IP-адреса, их необходимо добавить в подписку. Чтобы создать зарезервированный IP-адрес из пула общедоступных IP-адресов в *центральной части США*, выполните следующую команду PowerShell:

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

Обратите внимание, однако, что нельзя указать, какой IP-адрес будет зарезервирован. Чтобы просмотреть, какие IP-адреса зарезервированы в подписке, выполните следующую команду PowerShell и обратите внимание на значения *ReservedIPName* и *Address*.

	Get-AzureReservedIP

	ReservedIPName       : MyReservedIP
	Address              : 23.101.114.211
	Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
	Label                :
	Location             : Central US
	State                : Created
	InUse                : False
	ServiceName          :
	DeploymentName       :
	OperationDescription : Get-AzureReservedIP
	OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
	OperationStatus      : Succeeded

После того как IP-адрес будет зарезервирован, он останется связан с подпиской до тех пор, пока вы ее не удалите. Чтобы удалить показанный выше зарезервированный IP-адрес, выполните следующую команду PowerShell:

	Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## Как зарезервировать IP-адрес существующей облачной службы

Можно зарезервировать IP-адрес существующей облачной службы, добавив параметр *-ServiceName*. Чтобы зарезервировать IP-адрес облачной службы *TestService* в расположении *центральная часть США*, выполните следующую команду PowerShell:

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## Связывание зарезервированного IP-адреса с новой облачной службой
Приведенный ниже сценарий создает новый зарезервированный IP-адрес, а затем связывает его с новой облачной службой с именем *TestService*.

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] При создании зарезервированного IP-адреса для использования с облачной службой по-прежнему необходимо будет обращаться к виртуальной машине по ссылке *VIP:&lt;номер порта>* для входящей связи. Резервирование IP-адреса не означает возможность прямого подключения к виртуальной машине. Зарезервированный IP-адрес назначается облачной службе, в которой развернута виртуальная машина. Если требуется подключиться непосредственно к виртуальной машине по IP-адресу, следует настроить общедоступный IP-адрес уровня экземпляра. Общедоступный IP-адрес уровня экземпляра — это тип общедоступного IP-адреса (называемый ILPIP), который назначается непосредственно вашей виртуальной машине. Его нельзя зарезервировать. См. [Общедоступный IP-адрес уровня экземпляра (ILPIP)](virtual-networks-instance-level-public-ip.md) для получения дополнительных сведений.

## Удаление зарезервированного IP-адреса из работающей развернутой системы
Чтобы удалить зарезервированный IP-адрес, назначенный новой службе, созданной в приведенном выше сценарии, выполните следующую команду PowerShell:

	Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] При удалении зарезервированного IP-адреса из работающей развернутой системы резервирование не удаляется из вашей подписки. IP-адрес просто освобождается для использования другим ресурсом в подписке.

## Связывание зарезервированного IP-адреса с работающей развернутой системой
Приведенный ниже сценарий создает новую облачную службу с именем *TestService2* с новой виртуальной машиной *TestVM2*, а затем связывает существующий зарезервированный IP-адрес, именуемый *MyReservedIP*, с облачной службой.

	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| New-AzureVM -ServiceName TestService2 -Location "Central US"
	Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## Связывание зарезервированного IP-адреса с облачной службой с помощью файла конфигурации службы
Зарезервированный IP-адрес можно также связать с облачной службой с помощью файла конфигурации службы (CSCFG). В XML-коде ниже показано, как настроить облачную службу для использования зарезервированного виртуального IP-адреса *MyReservedIP*.

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
	  <Role name="WebRole1">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	    </ConfigurationSettings>
	  </Role>
	  <NetworkConfiguration>
	    <AddressAssignments>
	      <ReservedIPs>
	       <ReservedIP name="MyReservedIP"/>
	      </ReservedIPs>
	    </AddressAssignments>
	  </NetworkConfiguration>
	</ServiceConfiguration>

## Дальнейшие действия

- Чтобы узнать, как IP-адрес работает в классической модели развертывания, см. статью [IP-адреса в Azure (классическая модель развертывания)](virtual-network-ip-addresses-overview-classic.md).

- Ознакомьтесь с информацией о [зарезервированных частных IP-адресах](virtual-networks-reserved-private-ip.md).

- Ознакомьтесь с информацией об [общедоступных IP-адресах уровня экземпляра (ILPIP)](virtual-networks-instance-level-public-ip.md).

<!---HONumber=AcomDC_0824_2016-->
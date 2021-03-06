<properties
	pageTitle="Создание первой фабрики данных (шаблон Resource Manager) | Microsoft Azure"
	description="Работая с этим руководством, вы создадите образец конвейера фабрики данных Azure с помощью шаблона Azure Resource Manager."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/01/2016"
	ms.author="spelluru"/>

# Руководство. Создание фабрики данных Azure с помощью шаблона диспетчера ресурсов Azure
> [AZURE.SELECTOR]
- [Портал Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Шаблон Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [ИНТЕРФЕЙС REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


[AZURE.INCLUDE [data-factory-tutorial-prerequisites](../../includes/data-factory-tutorial-prerequisites.md)]

## Дополнительные требования
Кроме необходимых компонентов, перечисленных в разделе с предварительными требованиями, установите следующее:

- **Установите Azure PowerShell**. Чтобы установить последнюю версию Azure PowerShell на локальном компьютере, следуйте инструкциям в статье [Установка и настройка Azure PowerShell](../powershell-install-configure.md).
- Сведения о шаблонах Azure Resource Manager см. в статье [Создание шаблонов Azure Resource Manager](../resource-group-authoring-templates.md).

## Создание шаблона Resource Manager

Создайте в папке **C:\\ADFGetStarted** файл JSON с именем **ADFTutorialARM.json** со следующим содержимым:

Этот шаблон позволяет создавать следующие сущности фабрики данных:

1. **Фабрика данных** с именем **TutorialDataFactoryARM**. Фабрика данных может иметь один или несколько конвейеров. Конвейер может содержать одно или несколько действий. Это может быть, например, действие копирования, копирующее данные из исходного хранилища данных в целевое, или действие HDInsight Hive для выполнения скрипта Hive, преобразующего входные данные.
2. Две **связанные службы**: **StorageLinkedService** и **HDInsightOnDemandLinkedService**. Эти связанные службы связывают учетную запись хранения Azure и кластер Azure HDInsight по запросу с фабрикой данных. В этом примере учетная запись хранения Azure содержит входные и выходные данные для конвейера. Для выполнения скрипта Hive, указанного в действии конвейера, в этом примере используется связанная служба HDInsight. Определите, какие данные хранилища и службы вычислений используются в сценарии, и свяжите эти службы с фабрикой данных, создав связанные службы.
3. Два **набора данных** (входной и выходной): **AzureBlobInput** и **AzureBlobOutput**. Эти наборы данных представляют входные и выходные данные для обработки Hive. Эти наборы данных ссылаются на службу **StorageLinkedService**, созданную ранее в ходе работы с этим руководством. Точки связанной службы указывают на учетную запись хранения Azure, а наборы данных указывают контейнер, папку и имя файла в хранилище, в котором содержатся входные и выходные данные.

Щелкните вкладку **С помощью редактора фабрики данных**, чтобы перейти к статье с подробными сведениями о свойствах JSON, используемых в этом шаблоне.

> [AZURE.IMPORTANT] Измените значения переменных **storageAccountName** и **storageAccountKey**. Измените значение переменной **dataFactoryName**, так как имя должно быть уникальным.


	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<AZURE STORAGE ACCOUNT NAME>" ,
	        "storageAccountKey":  "<AZURE STORAGE ACCOUNT KEY>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "AzureStorageLinkedService",
	        "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
	        "blobInputDataset": "AzureBlobInput",
	        "blobOutputDataset": "AzureBlobOutput",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "westus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "linkedservices",
	                    "name": "[variables('storageLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "AzureStorage",
	                        "typeProperties": {
	                            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "linkedservices",
	                    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "HDInsightOnDemand",
        					"typeProperties": {
                                "clusterSize": 4,
                                "version":  "3.2",
            					"timeToLive": "00:05:00",
                                "osType": "windows",
            					"linkedServiceName": "[variables('storageLinkedServiceName')]",
    						}
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobInputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "type": "AzureBlob",
						        "linkedServiceName": "[variables('storageLinkedServiceName')]",
						        "typeProperties": {
						            "fileName": "input.log",
						            "folderPath": "adfgetstarted/inputdata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        },
						        "external": true,
						        "policy": {}
						    }
	                    },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobOutputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "published": false,
						        "type": "AzureBlob",
						        "linkedServiceName": "[variables('storageLinkedServiceName')]",
						        "typeProperties": {
						            "folderPath": "adfgetstarted/partitioneddata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        }
						    }
	                    },
	                    {
	                        "dependsOn": [
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "description": "My first Azure Data Factory pipeline",
						        "activities": [
						            {
						                "type": "HDInsightHive",
						                "typeProperties": {
						                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
						                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
						                    "defines": {
		                        				"inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
		                        				"partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
						                    }
						                },
						                "inputs": [
						                    {
						                        "name": "AzureBlobInput"
						                    }
						                ],
						                "outputs": [
						                    {
						                        "name": "AzureBlobOutput"
						                    }
						                ],
						                "policy": {
						                    "concurrency": 1,
						                    "retry": 3
						                },
						                "scheduler": {
						                    "frequency": "Month",
						                    "interval": 1
						                },
						                "name": "RunSampleHiveActivity",
						                "linkedServiceName": "HDInsightOnDemandLinkedService"
						            }
						        ],
						        "start": "2016-04-01T00:00:00Z",
						        "end": "2016-04-02T00:00:00Z",
						        "isPaused": false
						    }
	                    }
	            ]
	        }
	    ]
	}

Чтобы перейти к статье с подробными сведениями о свойствах JSON, используемых в этом шаблоне, щелкните вкладку **Редактор фабрики данных**.

Обратите внимание на следующее:

- С помощью вышеупомянутого файла JSON фабрика данных создает кластер HDInsight **под управлением Windows**. Можно также создать кластер HDInsight **под управлением Linux**. Дополнительные сведения см. в разделе [Связанная служба Azure HDInsight по запросу](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
- Вместо кластера HDInsight по запросу можно использовать **собственный кластер HDInsight**. Дополнительные сведения см. в разделе [Связанная служба Azure HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
- Кластер HDInsight создает **контейнер по умолчанию** в хранилище BLOB-объектов, указанном в коде JSON (**linkedServiceName**). При удалении кластера HDInsight этот контейнер не удаляется. В этом весь замысел. Если используется связанная служба HDInsight по запросу, кластер HDInsight создается всякий раз, когда нужно обработать срез данных (если не используется динамический кластер **timeToLive**), после чего кластер удаляется.

	По мере обработки новых срезов количество контейнеров в хранилище BLOB-объектов будет увеличиваться. Если эти контейнеры не используются для устранения неполадок с заданиями, удалите их — это позволит сократить расходы на хранение. Подобные контейнеры имеют имена в формате "adf**имя\_вашей\_фабрики\_данных**-**имя\_связанной\_службы**-метка\_даты\_и\_времени". Чтобы удалить контейнеры из хранилища BLOB-объектов Azure, используйте [обозреватель хранилищ Microsoft](http://storageexplorer.com/).

Дополнительные сведения см. в разделе [Связанная служба Azure HDInsight по запросу](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

> [AZURE.NOTE] Еще один пример шаблона Resource Manager для создания фабрики данных Azure можно найти на портале [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json).

## Создание фабрики данных

1. Откройте **Azure PowerShell** и выполните следующие команды.
	- Выполните командлет **Login-AzureRmAccount** и введите имя пользователя и пароль, которые используются для входа на портал Azure.
	- Чтобы выбрать подписку, в которой требуется создать фабрику данных, выполните команду Get-AzureRmSubscription -SubscriptionName <имя подписки> | Set-AzureRmContext.
1. Чтобы развернуть сущности фабрики данных с помощью шаблона Resource Manager, созданного на шаге 1, выполните следующую команду.

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Отслеживание конвейера
 
1.	Войдя на [портал Azure](https://portal.azure.com/), щелкните **Обзор** и выберите **Фабрики данных**. ![Просмотреть все -> Фабрики данных](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	В колонке **Фабрики данных** выберите созданную фабрику данных (**TutorialFactoryARM**).
2.	В колонке **Фабрика данных** для своей фабрики данных щелкните элемент **Схема**. ![Плитка «Схема»](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	В **представлении схемы** вы увидите все конвейеры и наборы данных, используемые в этом руководстве.
	
	 ![Представление схемы](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png)
8. В представлении схемы дважды щелкните набор данных **AzureBlobOutput**. Вы увидите срез, который сейчас обрабатывается.

	 ![Выборка](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Как только обработка завершится, срез перейдет в состояние **Готово**. Создание используемого по требованию кластера HDInsight обычно занимает некоторое время (около 20 минут).

	 ![Выборка](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)
10. Когда срез перейдет в состояние **Готово**, проверьте выходные данные в папке **partitioneddata** контейнера **adfgetstarted** в хранилище BLOB-объектов.

Инструкции по использованию колонок на портале Azure для мониторинга конвейера и наборов данных, созданных с помощью этого руководства, см. в статье [Мониторинг конвейеров фабрики данных Azure и управление ими](data-factory-monitor-manage-pipelines.md).

Вы также можете использовать приложение мониторинга и управления для мониторинга данных конвейеров. Дополнительные сведения об использовании этого приложения см. в статье [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью нового приложения по мониторингу и управлению](data-factory-monitor-manage-app.md).

> [AZURE.IMPORTANT] В случае успешной обработки среза входной файл удаляется. Если вы хотите повторно обработать срез или еще раз выполнить инструкции из руководства, передайте входной файл (input.log) в папку inputdata в контейнере adfgetstarted.

## Шаблон Resource Manager для создания шлюза
Ниже приведен пример шаблона Resource Manager для создания логического шлюза в серверной части. Установите шлюз на локальном компьютере или виртуальной машине IaaS Azure и зарегистрируйте его в службе фабрики данных с помощью ключа. Дополнительные сведения см. в статье [Перемещение данных между локальными источниками и облаком при помощи шлюза управления данными](data-factory-move-data-between-onprem-and-cloud.md).

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "GatewayUsingArmDF",
	        "apiVersion": "2015-10-01",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "eastus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "gateways",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "name": "GatewayUsingARM",
	                    "properties": {
	                    	"description": "my gateway"
						}
	                }            
				]
	        }
	    ]
	}

Этот шаблон создает фабрику данных с именем GatewayUsingArmDF и шлюз с именем GatewayUsingARM.

## См. также
| Раздел | Описание |
| :---- | :---- |
| [Действия по преобразованию данных](data-factory-data-transformation-activities.md) | В этой статье рассматриваются действия по преобразованию данных (например, преобразование HDInsight Hive, используемое в этом руководстве), поддерживаемые фабрикой данных Azure. |
| [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md) | Здесь объясняются аспекты планирования и исполнения в модели приложений фабрики данных. |
| [Конвейеры](data-factory-create-pipelines.md) | Эта статья поможет вам понять сущность конвейеров и действий в фабрике данных Azure, а также научиться с их помощью создавать комплексные рабочие процессы, управляемые данными, для конкретных бизнес-сценариев. |
| [Наборы данных](data-factory-create-datasets.md) | Эта статья поможет вам понять, что такое наборы данных в фабрике данных Azure.
| [Мониторинг конвейеров фабрики данных Azure и управление ими с помощью нового приложения по мониторингу и управлению](data-factory-monitor-manage-app.md) | В этой статье описывается мониторинг и отладка конвейеров, а также управление ими с помощью приложения мониторинга и управления. 

  

<!---HONumber=AcomDC_0914_2016-->
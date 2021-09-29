---
title: Erstellen einer Azure Integration Runtime-Instanz
titleSuffix: Azure Data Factory & Azure Synapse
description: Informationen zum Erstellen von Integration Runtime in Azure Data Factory und Azure Synapse Analytics, die zum Kopieren von Daten und Verteilen von Transformationsaktivitäten verwendet wird.
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.date: 09/09/2021
author: lrtoyou1223
ms.author: lle
ms.custom: devx-track-azurepowershell, synapse
ms.openlocfilehash: 063d4138a86fb5235562ebb0d2592640f370806e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124760314"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Erstellen und Konfigurieren von Azure Integration Runtime
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bei der Integrationslaufzeit (Integration Runtime, IR) handelt es sich um die Compute-Infrastruktur, mit der Azure Data Factory- und Synapse-Pipelines Datenintegrationsfunktionen übergreifend für verschiedene Netzwerkumgebungen bereitstellen. Weitere Informationen zu IR finden Sie unter [Integration runtime in Azure Data Factory](concepts-integration-runtime.md) (Integration Runtime in Azure Data Factory).

Azure IR bietet ein vollständig verwaltetes Compute zur nativen Durchführung des Verschiebens von Daten und Disponierens von Datentransformationsaktivitäten zum Berechnen von Diensten wie HDInsight. IR wird in der Azure-Umgebung gehostet und unterstützt das Herstellen von Verbindungen mit Ressourcen in einer öffentlichen Netzwerkumgebung mit öffentlich zugänglichen Endpunkten.

Dieses Dokument ist eine Einführung zum Erstellen und Konfigurieren von Azure Integration Runtime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Standardmäßige Azure IR
Standardmäßig verfügt jede Data Factory oder jeder Synapse Arbeitsbereich im Back-End über eine Azure Integration Runtime, die Vorgänge in Clouddatenspeichern und Compute-Diensten im öffentlichen Netzwerk unterstützt. Der Speicherort dieser Azure IR wird automatisch aufgelöst. Wenn die **connectVia**-Eigenschaft in der Definition des verknüpften Diensts nicht angegeben ist, wird die standardmäßige Azure IR verwendet. Sie müssen nur dann explizit eine Azure IR erstellen, wenn Sie den Speicherort der IR explizit definieren möchten, oder wenn Sie die Aktivitätsausführungen aus Verwaltungsgründen auf verschiedenen IRs virtuell gruppieren möchten. 

## <a name="create-azure-ir"></a>Erstellen der Azure IR

Zum Erstellen und Einrichten einer Azure IR können Sie die nachstehenden Verfahren verwenden.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Erstellen einer Azure IR über Azure PowerShell
Integration Runtime kann mithilfe des PowerShell-Cmdlets **Set-AzDataFactoryV2IntegrationRuntime** erstellt werden. Geben Sie zum Erstellen einer Azure IR den Namen, Speicherort und Typ im Befehl an. Hier ist ein Beispielbefehl zum Erstellen einer Azure IR mit Speicherort „Europa, Westen“:

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Für die Azure IR muss der Typ auf **Managed** festgelegt werden. Sie müssen keine Computedetails angeben, da sie in der Cloud vollständig elastisch verwaltet wird. Geben Sie Computedetails wie Knotengröße und Knotenzahl an, wenn Sie eine Azure-SSIS IR erstellen möchten. Weitere Informationen finden Sie unter [Create an Azure-SSIS integration runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md) (Erstellen einer Azure-SSIS Integration Runtime in Azure Data Factory).

Sie können eine vorhandenen Azure IR mithilfe des Set-AzDataFactoryV2IntegrationRuntime-PowerShell-Cmdlets konfigurieren, um ihren Speicherort zu ändern. Weitere Informationen über den Speicherort einer Azure IR finden Sie unter [Integration Runtime in Azure Data Factory](concepts-integration-runtime.md).

### <a name="create-an-azure-ir-via-ui"></a>Erstellen einer Azure IR über die Benutzeroberfläche
Führen Sie die unten angegebenen Schritte aus, um eine Azure IR über die Benutzeroberfläche zu erstellen.

1. Wählen Sie auf der Startseite für den Dienst im Bereich ganz links die [Registerkarte Verwalten](./author-management-hub.md) aus.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
    
    :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="Die Schaltfläche „Verwalten“ auf der Startseite":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/get-started-page-manage-button-synapse.png" alt-text="Schaltfläche „Verwalten“ auf der Startseite":::

2. Wählen Sie im linken Bereich **Integration Runtime** und dann **+Neu** aus.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/manage-new-integration-runtime.png" alt-text="Screenshot: Hervorgehobene Integration Runtimes im linken Bereich und die Schaltfläche „+ Neu“":::
   
    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/manage-new-integration-runtime-synapse.png" alt-text="Screenshot: Hervorgehobene Integration Runtimes im linken Bereich und die Schaltfläche „+ Neu“":::

3. Wählen Sie auf der Seite **Integration Runtime-Setup** die Option **Azure, Selbstgehostet** und dann **Weiter** aus. 

1. Wählen Sie auf der daraufhin angezeigten Seite die Option **Azure** zum Erstellen einer Azure IR und dann **Weiter** aus.
   :::image type="content" source="media/create-azure-integration-runtime/new-azure-integration-runtime.png" alt-text="Erstellen einer Integration Runtime":::

1. Geben Sie einen Namen für Ihre Azure IR ein, und wählen Sie **Erstellen** aus.
   :::image type="content" source="media/create-azure-integration-runtime/create-azure-integration-runtime.png" alt-text="Erstellen einer Azure IR":::

1. Wenn die Erstellung abgeschlossen ist, wird eine Popupbenachrichtigung angezeigt. Vergewissern Sie sich auf der Seite **Integration Runtimes**, dass die neu erstellte IR in der Liste angezeigt wird.

## <a name="use-azure-ir"></a>Verwenden der Azure IR

Sobald eine Azure IR erstellt ist, können Sie in Ihrer Definition des verknüpften Diensts darauf verweisen. Das folgende Beispiel zeigt Ihnen, wie Sie von einem verknüpften Azure Storage-Dienst aus auf die oben erstellte Azure Integration Runtime verweisen können:

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Nächste Schritte
Informationen zum Erstellen anderer Integration Runtime-Typen finden Sie in den folgenden Artikeln:

- [Create self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Erstellen einer selbstgehosteten Integrationslaufzeit)
- [Erstellen einer Azure SSIS Integration Runtime](create-azure-ssis-integration-runtime.md)

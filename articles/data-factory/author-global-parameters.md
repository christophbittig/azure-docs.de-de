---
title: Globale Parameter
description: Festlegen von globalen Parametern für jede Azure Data Factory-Umgebung
ms.service: data-factory
ms.subservice: authoring
ms.topic: conceptual
author: joshuha-msft
ms.author: joowen
ms.date: 05/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3205d5c3be7fe13cbaa4b881a3de6bffc15e9fd8
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550910"
---
# <a name="global-parameters-in-azure-data-factory"></a>Globale Parameter in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Globale Parameter sind Konstanten in einer Data Factory, die von einer Pipeline in beliebigen Ausdrücken verbraucht werden können. Sie sind hilfreich, wenn Sie über mehrere Pipelines mit identischen Parameternamen und -werten verfügen. Beim Höherstufen einer Data Factory per CI/CD-Prozess (Continuous Integration/Deployment) können Sie diese Parameter in einer Umgebung jeweils außer Kraft setzen. 

## <a name="creating-global-parameters"></a>Erstellen globaler Parameter

Navigieren Sie zum Erstellen eines globalen Parameters im Abschnitt **Verwalten** zur Registerkarte *Globale Parameter*. Wählen Sie **Neu** aus, um den Seitennavigationsbereich für die Erstellung zu öffnen.

:::image type="content" source="media/author-global-parameters/create-global-parameter-1.png" alt-text="Der Screenshot, auf dem die Schaltfläche „Neu“ hervorgehoben ist, die Sie zum Erstellen globaler Parameter auswählen.":::

Geben Sie im Seitennavigationsbereich einen Namen ein, wählen Sie einen Datentyp aus, und geben Sie den Wert Ihres Parameters an.

:::image type="content" source="media/author-global-parameters/create-global-parameter-2.png" alt-text="Der Screenshot zeigt, wo Sie den Namen, Datentyp und Wert für den neuen globalen Parameter hinzufügen.":::

Nachdem ein globaler Parameter erstellt wurde, können Sie ihn bearbeiten, indem Sie auf seinen Namen klicken. Wählen Sie zum gleichzeitigen Ändern mehrerer Parameter die Option **Edit all** (Alle bearbeiten) aus.

:::image type="content" source="media/author-global-parameters/create-global-parameter-3.png" alt-text="Erstellen globaler Parameter":::

## <a name="using-global-parameters-in-a-pipeline"></a>Verwenden globaler Parameter in einer Pipeline

Globale Parameter können in jedem [Pipelineausdruck](control-flow-expression-language-functions.md) verwendet werden. Wenn eine Pipeline auf eine andere Ressource verweist, z. B. ein Dataset oder einen Datenfluss, können Sie den Wert des globalen Parameters über die Parameter dieser Ressource an die untergeordneten Elemente übergeben. Auf globale Parameter wird wie folgt verwiesen: `pipeline().globalParameters.<parameterName>`.

:::image type="content" source="media/author-global-parameters/expression-global-parameters.png" alt-text="Verwenden von globalen Parametern":::

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> Globale Parameter in CI/CD

Es gibt zwei Möglichkeiten, globale Parameter in Ihre Continuous Integration- und Continuous Deployment-Lösung zu integrieren:

* Einschließen globaler Parameter in die ARM-Vorlage
* Bereitstellen globaler Parameter über ein PowerShell-Skript

Für allgemeine Anwendungsfälle empfiehlt es sich, globale Parameter in die ARM-Vorlage einzuschließen. Dies lässt sich nativ in die Lösung integrieren, die in der [CI/CD-Dokumentation](continuous-integration-delivery.md) beschrieben ist. Bei automatischer Veröffentlichung und Purview-Verbindung ist eine **PowerShell-Skriptmethode** erforderlich. Weitere Informationen zur PowerShell-Skriptmethode erhalten Sie im Folgenden. Globale Parameter werden standardmäßig als ARM-Vorlagenparameter hinzugefügt, da sie häufig zwischen Umgebungen geändert werden. Sie können das Einschließen globaler Parameter in die ARM-Vorlage über den Hub **Verwalten** aktivieren.

:::image type="content" source="media/author-global-parameters/include-arm-template.png" alt-text="Einschließen in eine ARM-Vorlage":::

> [!NOTE]
> Die Konfiguration **In ARM-Vorlage einschließen** ist nur im Git-Modus verfügbar. Im Livemodus und Data Factory-Modus ist sie derzeit deaktiviert. Verwenden Sie bei einer automatischen Veröffentlichung oder Purview-Verbindung nicht die Methode zum Einschließen globaler Parameter. Verwenden Sie die PowerShell-Skriptmethode. 

> [!WARNING]
>Sie können das Zeichen „-“ nicht im Parameternamen verwenden. Wenn Sie das tun, wird die Fehlermeldung „{"code":"BadRequest","message":"ErrorCode=InvalidTemplate,ErrorMessage=Der Ausdruck >'pipeline().globalParameters.myparam-dbtest-url' ist ungültig: .....}“ angezeigt. Stattdessen können Sie das Zeichen „_“ im Parameternamen verwenden. 

Durch das Hinzufügen globaler Parameter zur ARM-Vorlage wird eine Einstellung auf Factoryebene hinzugefügt, die andere Einstellungen auf Factoryebene in anderen Umgebungen außer Kraft setzt, z. B. einen kundenseitig verwalteten Schlüssel oder eine Git-Konfiguration. Wenn Sie diese Einstellungen in einer erweiterten Umgebung aktiviert haben, z. B. vom Typ UAT (Benutzerakzeptanztests) oder PRODUKTION, ist es besser, globale Parameter über ein PowerShell-Skript anhand der unten hervorgehobenen Schritte bereitzustellen. 


### <a name="deploying-using-powershell"></a>Bereitstellen mithilfe von PowerShell

In den folgenden Schritten wird beschrieben, wie Sie globale Parameter über PowerShell bereitstellen. Dies ist nützlich, wenn die Zielfactory eine Einstellung auf Factoryebene aufweist, z. B. einen kundenseitig verwalteten Schlüssel.

Beim Veröffentlichen einer Factory oder Exportieren einer ARM-Vorlage mit globalen Parametern wird der Ordner *globalParameters* mit der Datei *Ihr_Factory-Name_GlobalParameters.json* erstellt. Bei dieser Datei handelt es sich um ein JSON-Objekt, das jeden globalen Parametertyp und -wert der veröffentlichten Factory enthält.

:::image type="content" source="media/author-global-parameters/global-parameters-adf-publish.png" alt-text="Veröffentlichen von globalen Parametern":::

Bei einer Bereitstellung in einer neuen Umgebung, z. B. vom Typ TEST oder PRODUKTION, werden das Erstellen einer Kopie dieser Datei mit den globalen Parametern und das Überschreiben der entsprechenden umgebungsspezifischen Werte empfohlen. Beim erneuten Veröffentlichen wird die ursprüngliche Datei mit den globalen Parametern überschrieben, aber die Kopie für die andere Umgebung bleibt erhalten.

Wenn Sie beispielsweise über eine Factory mit dem Namen „ADF-DEV“ und einen globalen Parameter vom Typ „Zeichenfolge“ mit dem Namen „environment“ und dem Wert „dev“ verfügen, wird beim Veröffentlichen eine Datei mit dem Namen *ADF-DEV_GlobalParameters.json* generiert. Wenn Sie die Bereitstellung für eine Testfactory mit dem Namen „ADF_TEST“ durchführen, sollten Sie eine Kopie der JSON-Datei erstellen (z. B. mit dem Namen „ADF-TEST_GlobalParameters.json“) und die Parameterwerte durch die umgebungsspezifischen Werte ersetzen. Der Parameter „environment“ verfügt nun ggf. über den Wert „test“. 

:::image type="content" source="media/author-global-parameters/powershell-task.png" alt-text="Bereitstellen von globalen Parametern":::

Verwenden Sie das unten angegebene PowerShell-Skript, um globale Parameter für weitere Umgebungen bereitzustellen. Fügen Sie eine Azure PowerShell-DevOps-Aufgabe hinzu, bevor Sie die ARM-Vorlagenbereitstellung durchführen. In der DevOps-Aufgabe müssen Sie den Speicherort der neuen Parameterdatei, die Zielressourcengruppe und die Ziel-Data Factory angeben.

> [!NOTE]
> Zum Bereitstellen von globalen Parametern mit PowerShell müssen Sie mindestens Version 4.4.0 des Az-Moduls verwenden.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über den [CI/CD-Prozess (Continuous Integration/Deployment)](continuous-integration-delivery-improvements.md) von Azure Data Factory.
* Informieren Sie sich über die Nutzung der [Ausdruckssprache für die Ablaufsteuerung](control-flow-expression-language-functions.md).

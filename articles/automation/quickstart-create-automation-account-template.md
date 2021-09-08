---
title: Erstellen eines Automation-Kontos – Azure-Vorlage
titleSuffix: Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie ein Automation-Konto per Azure Resource Manager-Vorlage erstellen.
services: automation
ms.author: magoedte
ms.date: 07/20/2021
ms.topic: conceptual
ms.workload: infrastructure-services
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: b17bb61230fa06acc988129bd593ab5c25332e84
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727374"
---
# <a name="create-an-automation-account-by-using-arm-template"></a>Erstellen eines Automation-Kontos mithilfe einer ARM-Vorlage

Mit Azure Automation wird ein cloudbasierter Automatisierungs- und Konfigurationsdienst bereitgestellt, der eine einheitliche Verwaltung Ihrer Azure- und Nicht-Azure-Umgebungen unterstützt. Dieser Artikel zeigt Ihnen, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) bereitstellen, die ein Automation-Konto erstellt. Bei Verwendung einer ARM-Vorlage werden im Vergleich zu anderen Bereitstellungsmethoden weniger Schritte benötigt.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.automation%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Diese Beispielvorlage führt Folgendes aus:

* Automatisieren der Erstellung eines Azure Monitor Log Analytics-Arbeitsbereichs
* Automatisieren der Erstellung eines Azure Automation-Kontos
* Verknüpfen des Automation-Kontos mit dem Log Analytics-Arbeitsbereich
* Hinzufügen von Automation-Beispielrunbooks zum Konto.

>[!NOTE]
>Die Erstellung des ausführenden Automation-Kontos wird bei Verwendung einer ARM-Vorlage nicht unterstützt. Informationen zum Erstellen eines ausführenden Kontos über das Portal oder mit PowerShell finden Sie unter [Erstellen eines ausführenden Kontos](create-run-as-account.md).

Nachdem Sie diese Schritte ausgeführt haben, müssen Sie [Diagnoseeinstellungen konfigurieren](automation-manage-send-joblogs-log-analytics.md), damit Ihr Automation-Konto Auftragsstatus und Auftragsdatenströme von Runbooks an den verknüpften Log Analytics Arbeitsbereich sendet.

Die in diesem Artikel verwendete Vorlage stammt aus den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.automation/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>API-Versionen

Die folgende Tabelle enthält die API-Versionen für die Ressourcen, die in diesem Beispiel verwendet werden.

| Resource | Ressourcentyp | API-Version |
|:---|:---|:---|
| [Arbeitsbereich](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [Automation-Konto](/azure/templates/microsoft.automation/automationaccounts) | automation | 2020-01-13-preview |
| [Mit dem Arbeitsbereich verknüpfte Dienste](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 2020-03-01-preview |

### <a name="before-you-use-the-template"></a>Vor der Verwendung der Vorlage

Die JSON-Parametervorlage ist so konfiguriert, dass Sie Folgendes angeben können:

* Den Namen des Arbeitsbereichs
* Die Region, in der der Arbeitsbereich erstellt werden soll
* Der Namen des Automation-Kontos.
* Die Region, in der das Automation-Konto erstellt werden soll

Die folgenden Parameter in der Vorlage werden mit einem Standardwert für den Log Analytics-Arbeitsbereich festgelegt:

* *sku:* Als Standardwert wird der neue, im Preismodell von April 2018 veröffentlichte Tarif pro GB verwendet.
* *dataRetention* ist standardmäßig auf 30 Tage festgelegt.

>[!WARNING]
>Wenn Sie einen Log Analytics-Arbeitsbereich in einem Abonnement mit dem Preismodell von April 2018 erstellen oder konfigurieren möchten, ist *PerGB2018* als einziger gültiger Log Analytics-Tarif verfügbar.
>

Die JSON-Vorlage gibt einen Standardwerte für die anderen Parameter an, die wahrscheinlich als Standardkonfiguration in Ihrer Umgebung verwendet werden. Für den gemeinsamen Zugriff in Ihrer Organisation können Sie die Vorlage in einem Azure Storage-Konto speichern. Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure-Befehlszeilenschnittstelle](../azure-resource-manager/templates/deploy-cli.md).

Wenn Sie mit Azure Automation und Azure Monitor noch nicht vertraut sind, sollten Sie unbedingt die folgenden Konfigurationsdetails kennen. Sie können Ihnen helfen, Fehler zu vermeiden, wenn Sie versuchen, einen mit Ihrem neuen Automation-Konto verknüpften Log Analytics-Arbeitsbereich zu erstellen, zu konfigurieren und zu verwenden.

* Lesen Sie die [zusätzlichen Details](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), um die Optionen für die Arbeitsbereichskonfiguration vollständig zu verstehen, z. B. Zugriffssteuerungsmodus, Tarif, Datenaufbewahrung und Kapazitätsreservierungsstufe.

* Überprüfen Sie die [Arbeitsbereichszuordnungen](how-to/region-mappings.md), um die unterstützten Regionen inline oder in einer Parameterdatei anzugeben. Es werden nur bestimmte Regionen zum Verknüpfen mit einem Log Analytics-Arbeitsbereich und einem Automation-Konto in Ihrem Abonnement unterstützt.

* Wenn Sie noch nicht mit Azure Monitor-Protokollen vertraut sind und noch keinen Arbeitsbereich bereitgestellt haben, sollten Sie die [Entwurfsanleitungen für Arbeitsbereiche](../azure-monitor/logs/design-logs-deployment.md) lesen. Sie helfen Ihnen, etwas über die Zugriffssteuerung zu erfahren und die Implementierungsstrategien für den Entwurf zu verstehen, die für Ihre Organisation empfohlen werden.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Klicken Sie auf das folgende Bild, um sich bei Azure anzumelden und eine Vorlage zu öffnen. Die Vorlage erstellt ein Azure Automation-Konto, einen Log Analytics-Arbeitsbereich und verknüpft das Automation-Konto mit dem Arbeitsbereich.

    [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.automation%2F101-automation%2Fazuredeploy.json)

2. Gehen Sie die Werte ein.

    Wenn Sie versuchen, die ARM-Vorlage über PowerShell, die Befehlszeilenschnittstelle oder das Vorlagenfeature im Portal auszuführen, erhalten Sie eine Fehlermeldung ähnlich der folgenden, wenn der `_artifactsLocation`-Parameter nicht ordnungsgemäß festgelegt ist:

    `"message": "Deployment template validation failed: 'The template resource '_artifactsLocation' at line '96' and column '31' is not valid: The language expression property 'templateLink' doesn't exist, available properties are 'template, templateHash, parameters, mode, debugSetting, provisioningState'.. Please see https://aka.ms/arm-template-expressions for usage details.'."`

    Um dies zu verhindern, geben Sie beim Ausführen über das Vorlagenfeature im Portal Folgendes für den `_artifactsLocation`-Parameter an: `https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.

    Bei der Ausführung über PowerShell schließen Sie den Parameter und dessen Wert ein: `-TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.

    Bei der Ausführung über die Azure-Befehlszeilenschnittstelle schließen Sie den Parameter und dessen Wert ein: `--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.

    Weitere Informationen zu PowerShell und zur Befehlszeilenschnittstelle finden Sie im Abschnitt **Verwenden der Vorlage** unter [Erstellen eines Azure Automation-Kontos (microsoft.com)](https://azure.microsoft.com/resources/templates/101-automation/).

3. Die Bereitstellung kann einige Minuten dauern. Nach Abschluss des Vorgangs sieht die Ausgabe in etwa wie folgt aus:

    ![Beispielergebnis nach abgeschlossener Bereitstellung](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Öffnen Sie im Azure-Portal das gerade erstellte Automation-Konto.

3. Wählen Sie im linken Bereich **Runbooks** aus. Auf der Seite **Runbooks** werden drei Tutorial-Runbooks aufgelistet, die mit dem Automation-Konto erstellt wurden.

    ![Mit dem Automation-Konto erstellte Tutorial-Runbooks](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. Wählen Sie im linken Bereich **Verknüpfter Arbeitsbereich** aus. Auf der Seite **Verknüpfter Arbeitsbereich** wird der Log Analytics-Arbeitsbereich angezeigt, den Sie zuvor als mit Ihrem Automation-Konto verknüpft angegeben haben.

    ![Mit dem Log Analytics-Arbeitsbereich verknüpftes Automation-Konto](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, trennen Sie das Automation-Konto von dem Log Analytics-Arbeitsbereich, und löschen Sie dann das Automation-Konto und den Arbeitsbereich.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie ein Automation-Konto und einen Log Analytics-Arbeitsbereich erstellt und diese miteinander verknüpft.

Weitere Informationen finden Sie in den Tutorials zu Azure Automation.

> [!div class="nextstepaction"]
> [Azure Automation-Tutorials](learn/automation-tutorial-runbook-graphical.md)

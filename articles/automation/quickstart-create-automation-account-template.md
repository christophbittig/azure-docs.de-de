---
title: Erstellen eines Azure Automation-Kontos mithilfe einer Resource Manager-Vorlage
titleSuffix: Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie ein Automation-Konto per Azure Resource Manager-Vorlage erstellen.
services: automation
ms.date: 08/27/2021
ms.topic: conceptual
ms.workload: infrastructure-services
ms.custom: mvc, subject-armqs, mode-arm
ms.openlocfilehash: 66fb9a88761b47f0cc704fcf20dd29dd7768777a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487884"
---
# <a name="create-an-azure-automation-account-using-a-resource-manager-template"></a>Erstellen eines Azure Automation-Kontos mithilfe einer Resource Manager-Vorlage

Mit Azure Automation wird ein cloudbasierter Automatisierungs- und Konfigurationsdienst bereitgestellt, der eine einheitliche Verwaltung Ihrer Azure- und Nicht-Azure-Umgebungen unterstützt. Dieser Artikel zeigt Ihnen, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) bereitstellen, die ein Automation-Konto erstellt. Bei Verwendung einer ARM-Vorlage werden im Vergleich zu anderen Bereitstellungsmethoden weniger Schritte benötigt. Die JSON-Vorlage gibt Standardwerte für Parameter an, die wahrscheinlich als Standardkonfiguration in Ihrer Umgebung verwendet werden. Für den gemeinsamen Zugriff in Ihrer Organisation können Sie die Vorlage in einem Azure Storage-Konto speichern. Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure-Befehlszeilenschnittstelle](../azure-resource-manager/templates/deploy-cli.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Mit dieser Beispielvorlage werden die folgenden Schritte ausgeführt:

* Automatisieren der Erstellung eines Azure Monitor Log Analytics-Arbeitsbereichs
* Automatisieren der Erstellung eines Azure Automation-Kontos
* Verknüpfen des Automation-Kontos mit dem Log Analytics-Arbeitsbereich
* Hinzufügen von Automation-Beispielrunbooks zum Konto.

> [!NOTE]
> Die Erstellung des ausführenden Automation-Kontos wird bei Verwendung einer ARM-Vorlage nicht unterstützt. Informationen zum Erstellen eines ausführenden Kontos über das Portal oder mit PowerShell finden Sie unter [Erstellen eines ausführenden Kontos](create-run-as-account.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie mit Azure Automation und Azure Monitor noch nicht vertraut sind, sollten Sie unbedingt die Konfigurationsdetails kennen. Dieses Verständnis kann Ihnen helfen, Fehler zu vermeiden, wenn Sie versuchen, einen mit Ihrem neuen Automation-Konto verknüpften Log Analytics-Arbeitsbereich zu erstellen, zu konfigurieren und zu verwenden.

* Lesen Sie die [zusätzlichen Details](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), um die Optionen für die Arbeitsbereichskonfiguration vollständig zu verstehen, z. B. Zugriffssteuerungsmodus, Tarif, Datenaufbewahrung und Kapazitätsreservierungsstufe.

* Überprüfen Sie die [Arbeitsbereichszuordnungen](how-to/region-mappings.md), um die unterstützten Regionen inline oder in einer Parameterdatei anzugeben. Es werden nur bestimmte Regionen zum Verknüpfen mit einem Log Analytics-Arbeitsbereich und einem Automation-Konto in Ihrem Abonnement unterstützt.

* Wenn Sie noch nicht mit Azure Monitor-Protokollen vertraut sind und noch keinen Arbeitsbereich bereitgestellt haben, lesen Sie die [Entwurfsanleitungen für Arbeitsbereiche](../azure-monitor/logs/design-logs-deployment.md). Dieses Dokument hilft Ihnen, etwas über die Zugriffssteuerung zu erfahren und die empfohlenen Implementierungsstrategien für den Entwurf Ihrer Organisation zu verstehen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in diesem Artikel verwendete Vorlage stammt aus den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.automation/101-automation/azuredeploy.json":::

In der Vorlage sind die folgenden Azure-Ressourcen definiert:

* [**Microsoft.OperationalInsights/workspaces**](/azure/templates/microsoft.operationalinsights/workspaces): Erstellt einen Azure Log Analytics-Arbeitsbereich.
* [**Microsoft.Automation/AutomationAccounts**](/azure/templates/microsoft.automation/automationaccounts): Erstellt ein Azure Automation-Konto.
* [**Microsoft.Automation/AutomationAccounts/runbooks**](/azure/templates/microsoft.automation/automationaccounts/runbooks): Erstellt ein Runbook für ein Azure Automation-Konto.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie unten die Schaltfläche **In Azure bereitstellen** aus, um sich bei Azure anzumelden und die ARM-Vorlage zu öffnen.

    [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.automation%2F101-automation%2Fazuredeploy.json)

1. Geben Sie folgende Werte ein bzw. wählen diese aus:

    |Eigenschaft |BESCHREIBUNG |
    |---|---|
    |Subscription |Wählen Sie in der Dropdownliste Ihr Azure-Abonnement aus.|
    |Resource group |Wählen Sie in der Dropdownliste Ihre vorhandene Ressourcengruppe oder die Option **Neu erstellen** aus.|
    |Region |Dieser Wert wird automatisch aufgefüllt.|
    |Arbeitsbereichname |Geben Sie einen Namen für Ihren neuen Log Analytics-Arbeitsbereich ein.|
    |Sku | Als Standardwert wird der neue, im Preismodell von April 2018 veröffentlichte Tarif pro GB verwendet. Wenn Sie einen Log Analytics-Arbeitsbereich in einem Abonnement mit dem Preismodell von April 2018 erstellen oder konfigurieren möchten, ist `PerGB2018` als einziger gültiger Log Analytics-Tarif verfügbar.|
    |Beibehaltung von Daten |Der Standardwert ist 30 Tage.|
    |Standort |Als Wert wird automatisch der Speicherort für die Ressourcengruppe eingefügt.|
    |Name des Automation-Kontos | Geben Sie einen Namen für Ihr neues Automation-Konto ein.|
    |Beispielname eines grafischen Runbooks | Nehmen Sie keine Änderung vor.|
    |Beispielbeschreibung eines grafischen Runbooks | Nehmen Sie keine Änderung vor.|
    |Beispielname eines PowerShell-Runbooks | Nehmen Sie keine Änderung vor.|
    |Beispielbeschreibung eines PowerShell-Runbooks | Nehmen Sie keine Änderung vor.|
    |Beispielname für Python2Runbook |Nehmen Sie keine Änderung vor.|
    |Beispielbeschreibung für Python2Runbook |Nehmen Sie keine Änderung vor.|
    |„_artifacts“-Speicherort |Nehmen Sie keine Änderung vor. <sup>*</sup> URI zum Artefaktespeicherort.|
    |SAS-Token für „_artifacts“-Speicherort | Lassen Sie dieses Feld leer. Das für den Zugriff auf `_artifactsLocation` erforderliche „sasToken“. Wenn die Vorlage mit den zugehörigen Skripten bereitgestellt wird, wird ein `sasToken` automatisch generiert.|

    <sup>*</sup> Wenn Sie versuchen, die ARM-Vorlage über PowerShell, die Befehlszeilenschnittstelle oder das Vorlagenfeature im Portal auszuführen, erhalten Sie eine Fehlermeldung ähnlich der folgenden, wenn der `_artifactsLocation`-Parameter nicht ordnungsgemäß festgelegt ist:
    
    `"message": "Deployment template validation failed: 'The template resource '_artifactsLocation' at line '96' and column '31' is not valid: The language expression property 'templateLink' doesn't exist, available properties are 'template, templateHash, parameters, mode, debugSetting, provisioningState'.. Please see https://aka.ms/arm-template-expressions for usage details.'."`
    
    Um diesen Fehler zu verhindern, geben Sie beim Ausführen über das Vorlagenfeature im Portal folgenden Wert für den `_artifactsLocation`-Parameter an: `https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.
    
    Bei der Ausführung über PowerShell schließen Sie den Parameter und dessen Wert ein: `-TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.
    
    Bei der Ausführung über die Azure-Befehlszeilenschnittstelle schließen Sie den Parameter und dessen Wert ein: `--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.automation/101-automation/azuredeploy.json`.
    
    Weitere Informationen zu PowerShell und zur Befehlszeilenschnittstelle finden Sie im Abschnitt **Verwenden der Vorlage** unter [Erstellen eines Azure Automation-Kontos (microsoft.com)](https://azure.microsoft.com/resources/templates/101-automation/).

1. Wählen Sie **Überprüfen + erstellen** und danach **Erstellen** aus. Die Bereitstellung kann einige Minuten dauern. Nach Abschluss des Vorgangs sieht die Ausgabe in etwa wie in der folgenden Abbildung aus:

    ![Beispielergebnis nach abgeschlossener Bereitstellung](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

1. Nach Abschluss der Bereitstellung erhalten Sie die Benachrichtigung **Bereitstellung erfolgreich** mit dem Link **Zu Ressource wechseln**. Auf Ihrer Seite der **Ressourcengruppe** werden Ihre neuen Ressourcen aufgeführt. Wählen Sie in der Liste Ihr neues Automation-Konto aus.

1. Wählen Sie links unter **Prozessautomatisierung** die Option **Runbooks** aus. Auf der Seite **Runbooks** werden die drei Beispielrunbooks aufgelistet, die mit dem Automation-Konto erstellt wurden.

    ![Mit dem Automation-Konto erstellte Tutorial-Runbooks](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

1. Wählen Sie links unter **Verwandte Ressourcen** die Option **Verknüpfter Arbeitsbereich** aus. Auf der Seite **Verknüpfter Arbeitsbereich** wird der zuvor von Ihnen angegebene Log Analytics-Arbeitsbereich angezeigt, der mit Ihrem Automation-Konto verknüpft ist.

    ![Mit dem Log Analytics-Arbeitsbereich verknüpftes Automation-Konto](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren Sie Diagnoseeinstellungen](automation-manage-send-joblogs-log-analytics.md), damit Ihr Automation-Konto Auftragsstatus und Auftragsdatenströme von Runbooks an den verknüpften Log Analytics Arbeitsbereich sendet.

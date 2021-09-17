---
title: 'Schnellstart: Erstellen und Verwalten von Workflows mit Azure PowerShell in mehrinstanzenfähigen Azure Logic Apps'
description: Erstellen und verwalten Sie Logic Apps-Workflows mithilfe der PowerShell in mehrinstanzenfähigen Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell, contperf-fy21q2
ms.date: 07/26/2021
ms.openlocfilehash: 42f6ac6ce06f1b852af2027e6ef79f347d8050a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121784880"
---
# <a name="quickstart-create-and-manage-workflows-using-azure-powershell-in-multi-tenant-azure-logic-apps"></a>Schnellstart: Erstellen und Verwalten von Workflows mit Azure PowerShell in mehrinstanzenfähigen Azure Logic Apps

In dieser Schnellstartanleitung erfahren Sie, wie Sie Logik-Apps mithilfe der [Azure PowerShell](/powershell/azure/install-az-ps) erstellen und verwalten. In PowerShell können Sie eine Logik-App erstellen und dabei die JSON-Datei für eine Logik-App-Workflowdefinition verwenden. Anschließend können Sie Ihre Logik-App verwalten, indem Sie die Cmdlets im PowerShell-Modul [Az.LogicApp](/powershell/module/az.logicapp/) ausführen.

Wenn Sie noch nicht mit Azure Logic Apps vertraut sind, können Sie sich auch darüber informieren, wie Sie Ihre ersten Logik-Apps [über das Azure-Portal](quickstart-create-first-logic-app-workflow.md), [in Visual Studio](quickstart-create-logic-apps-with-visual-studio.md) und [in Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Das auf Ihrem lokalen Computer installierte [PowerShell-Az-Modul](/powershell/azure/install-az-ps).
* Eine [Azure-Ressourcengruppe](#example---create-resource-group) für die Erstellung Ihrer Logik-App.

### <a name="prerequisite-check"></a>Prüfen der Voraussetzungen

Überprüfen Sie Ihre Umgebung, bevor Sie beginnen:

* Melden Sie sich beim Azure-Portal an, und führen Sie [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) aus, um sich zu vergewissern, dass Ihr Abonnement aktiv ist.
* Überprüfen Sie Ihre Version von Azure PowerShell, indem Sie `Get-InstalledModule -Name Az` ausführen. Die neueste Version finden Sie unter [Versionshinweise für die Azure CLI](/powershell/azure/migrate-az-6.0.0).
  * Wenn Sie nicht über die neueste Version verfügen, aktualisieren Sie Ihre Installation, indem Sie [Aktualisieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps#update-the-azure-powershell-module) befolgen.

### <a name="example---create-resource-group"></a>Beispiel: Erstellen einer Ressourcengruppe

Sollten Sie noch nicht über eine Ressourcengruppe für Ihre Logik-App verfügen, erstellen Sie die Gruppe mithilfe des Cmdlets [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Mit dem folgenden Befehl wird beispielsweise eine Ressourcengruppe namens `testResourceGroup` am Standort `westus` erstellt:

```azurepowershell-interactive
New-AzResourceGroup -Name testResourceGroup -Location westus
```

In der Ausgabe wird `ProvisioningState` als `Succeeded` angezeigt, wenn Ihre Ressourcengruppe erfolgreich erstellt wurde:

```Output
ResourceGroupName : testResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup
```

## <a name="workflow-definition"></a>Workflowdefinition

Zum [Erstellen einer neuen Logik-App](#create-logic-apps-from-powershell) oder zum [Aktualisieren einer vorhandenen Logik-App](#update-logic-apps-from-powershell) mittels Azure PowerShell benötigen Sie eine Workflowdefinition für Ihre Logik-App. Im Azure-Portal können Sie die zugrunde liegende Workflowdefinition Ihrer Logik-App im JSON-Format anzeigen, indem Sie von der Ansicht **Designeransicht** zur **Codeansicht** wechseln.

Beim Ausführen der Befehle zum Erstellen oder Aktualisieren Ihrer Logik-App wird Ihre Workflowdefinition als erforderlicher Parameter hochgeladen (`Definition` oder `DefinitionFilePath`), je nach festgelegtem Parameter. Die Workflowdefinition muss als JSON-Datei erstellt werden, die den Angaben im [Schemareferenzleitfaden für die Workflowdefinitionssprache in Azure Logic Apps](./logic-apps-workflow-definition-language.md) entspricht.

## <a name="create-logic-apps-from-powershell"></a>Erstellen von Logik-Apps mit PowerShell

Für die Erstellung eines Logik-App-Workflows über Azure PowerShell können Sie das Cmdlet [`New-AzLogicApp`](/powershell/module/az.logicapp/new-azlogicapp) mit einer JSON-Datei für die Definition verwenden.

### <a name="example---create-logic-app"></a>Beispiel: Erstellen einer Logik-App

In diesem Beispiel wird ein Workflow namens `testLogicApp` in der Ressourcengruppe `testResourceGroup` am Standort `westus` erstellt. Die JSON-Datei `testDefinition.json` enthält die Workflowdefinition.

```azurepowershell-interactive
New-AzLogicApp -ResourceGroupName testResourceGroup -Location westus -Name testLogicApp -DefinitionFilePath .\testDefinition.json
```

Wenn Ihr Workflow erfolgreich erstellt wurde, zeigt PowerShell Ihre neue Workflowdefinition an.

## <a name="update-logic-apps-from-powershell"></a>Aktualisieren von Logik-Apps mit PowerShell

Der Workflow einer Logik-App kann über Azure PowerShell auch aktualisiert werden. Verwenden Sie hierzu das Cmdlet [`Set-AzLogicApp`](/powershell/module/az.logicapp/set-azlogicapp).

### <a name="example---update-logic-app"></a>Beispiel: Aktualisieren einer Logik-App

Dieses Beispiel zeigt, wie Sie den [im vorherigen Abschnitt erstellte Beispielworkflow](#example---create-logic-app) mithilfe einer anderen JSON-Definitionsdatei, `newTestDefinition.json`, aktualisieren.

```azurepowershell-interactive
Set-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp -DefinitionFilePath .\newTestDefinition.json
```

Wenn Ihr Workflow erfolgreich aktualisiert wurde, wird von PowerShell die aktualisierte Workflowdefinition Ihrer Logik-App angezeigt.

## <a name="delete-logic-apps-from-powershell"></a>Löschen von Logik-Apps mit PowerShell

Der Workflow einer Logik-App kann über Azure PowerShell auch gelöscht werden. Verwenden Sie hierzu das Cmdlet [`Remove-AzLogicApp`](/powershell/module/az.logicapp/remove-azlogicapp).

### <a name="example---delete-logic-app"></a>Beispiel: Löschen einer Logik-App

In diesem Beispiel wird der [in einem vorherigen Abschnitt erstellte Beispielworkflow](#example---create-logic-app) gelöscht.

```azurepowershell-interactive
Remove-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp
```

Nachdem Sie auf die Bestätigungsaufforderung mit `y` reagiert haben, wird die Logik-App gelöscht.

### <a name="considerations---delete-logic-app"></a>Überlegungen: Löschen einer Logik-App

Das Löschen einer Logik-App wirkt sich wie folgt auf Workflow-Instanzen aus:

* Der Logic Apps-Dienst unterbricht alle aktiven und ausstehenden Ausführungen so gut wie möglich.

  Selbst bei einer großen Menge oder einem umfangreichen Backlog werden die meisten Ausführungen abgebrochen, bevor sie abgeschlossen oder gestartet werden. Es kann jedoch einige Zeit dauern, bis der Abbruchvorgang abgeschlossen ist. In der Zwischenzeit werden möglicherweise einige Ausführungen gestartet, während die Runtime den Abbruchprozess durchläuft.

* Der Logic Apps-Dienst erstellt keine neuen Workflowinstanzen und führt keine neuen Workflowinstanzen aus.

* Wenn Sie einen Workflow löschen und dann denselben Workflow neu erstellen, hat der neu erstellte Workflow nicht die gleichen Metadaten wie der gelöschte Workflow. Sie müssen jeden Workflow, der den gelöschten Workflow aufgerufen hat, neu speichern. Auf diese Weise ruft der Aufrufer die richtigen Informationen für den neu erstellten Workflow ab. Andernfalls schlagen Aufrufe des neu erstellten Workflows mit einem `Unauthorized`-Fehler fehl. Dieses Verhalten gilt auch für Workflows, die Artefakte in Integrationskonten und Workflows verwenden, welche Azure-Funktionen aufrufen.

## <a name="show-logic-apps-in-powershell"></a>Anzeigen von Logik-Apps in PowerShell

Ein bestimmter Logik-App-Workflow kann mithilfe des Befehls [`Get-AzLogicApp`](/powershell/module/az.logicapp/get-azlogicapp) abgerufen werden.

### <a name="example---get-logic-app"></a>Beispiel: Abrufen einer Logik-App

In diesem Beispiel wird die Logik-App `testLogicApp` in der Ressourcengruppe `testResourceGroup` zurückgegeben.

```azurepowershell-interactive
Get-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).

Weitere Skriptbeispiele für Logik-Apps finden Sie [Browser für Codebeispiele von Microsoft](/samples/browse/?products=azure-logic-apps).
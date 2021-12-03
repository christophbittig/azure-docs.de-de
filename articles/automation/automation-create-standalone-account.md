---
title: Erstellen eines eigenständigen Azure Automation-Kontos
description: In diesem Artikel wird erläutert, wie Sie ein eigenständiges Azure Automation-Konto erstellen können.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: eedee6af181a0dc99635dc455dfda6a154fe5eb9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451038"
---
# <a name="create-a-standalone-azure-automation-account"></a>Erstellen eines eigenständigen Azure Automation-Kontos

In diesem Artikel erfahren Sie, wie Sie ein Azure Automation-Konto im Azure-Portal erstellen. Mit dem Automation-Konto können Sie Automation auswerten und sich damit vertraut machen, ohne zusätzliche Verwaltungsfeatures zu verwenden oder Azure Monitor-Protokolle zu integrieren. Wenn Sie zu einem späteren Zeitpunkt Runbookaufträge genauer überwachen möchten, können Sie jederzeit entsprechende Verwaltungsfeatures hinzufügen oder eine Integration von Azure Monitor-Protokollen durchführen.

Mit einem Automation-Konto können Sie Runbooks authentifizieren, indem Sie Ressourcen im Azure Resource Manager-Bereitstellungsmodell oder im klassischen Bereitstellungsmodell verwalten. Ein Automation-Konto kann Ressourcen in allen Regionen und Abonnements für einen bestimmten Mandanten verwalten.

Dank der automatischen Erstellung dieses Kontos können Sie schnell Runbooks für Ihre Automatisierungsanforderungen erstellen und bereitstellen.

## <a name="permissions-required-to-create-an-automation-account"></a>Erforderliche Berechtigungen zum Erstellen eines Automation-Kontos

Zum Erstellen oder Aktualisieren eines Automation-Kontos und zum Abschließen der Aufgaben in diesem Artikel müssen Sie über die folgenden Berechtigungen verfügen:

Für die Erstellung eines Automation-Kontos muss Ihr Azure AD-Benutzerkonto einer Rolle mit Berechtigungen hinzugefügt werden, die der Rolle „Besitzer“ für `Microsoft.Automation`-Ressourcen entsprechen. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure Automation](automation-role-based-access-control.md).

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Erstellen eines neuen Automation-Kontos über das Azure-Portal

Führen Sie die folgenden Schritte aus, um ein Azure Automation-Konto über das Azure-Portal zu erstellen:

1. Melden Sie sich mit einem Konto beim Azure-Portal an, das Mitglied der Rolle „Administratoren“ des Abonnements ist und als Co-Administrator des Abonnements fungiert.
1. Klicken Sie auf **+ Ressource erstellen**.
1. Suchen Sie nach **Automation**. Klicken Sie in den Suchergebnissen auf **Automation**.

   :::image type="content" source="./media/automation-create-standalone-account/automation-account-portal.png" alt-text="Suchen von Automation-Konten im Portal":::

Die Optionen für Ihr neues Automation-Konto sind auf der Seite **Erstellen eines Automation-Kontos** auf Registerkarten organisiert. In den folgenden Abschnitten werden die einzelnen Registerkarten und ihre Optionen beschrieben.

### <a name="basics"></a>Grundlagen

Geben Sie auf der Registerkarte **Grundlagen** die grundlegenden Informationen für Ihr Automation-Konto an. Nachdem Sie die Registerkarte **Grundlagen** ausgefüllt haben, können Sie Ihr neues Automation-Konto anhand der Optionen auf den übrigen Registerkarten weiter anpassen, oder Sie können **Überprüfen + erstellen** auswählen, um die Standardoptionen zu übernehmen und mit dem Überprüfen und dem Erstellen des Kontos fortzufahren.

> [!NOTE]
> Standardmäßig wird eine systemseitig zugewiesene verwaltete Identität für das Automation-Konto aktiviert.

In der folgenden Tabelle werden die Felder der Registerkarte **Grundlagen** beschrieben.

| **Feld** | **Erforderlich**<br> **or**<br> **optional** |**Beschreibung** |
|---|---|---|
|Subscription|Erforderlich |Wählen Sie in der Dropdownliste das Azure-Abonnement für das Konto aus.|
|Ressourcengruppe|Erforderlich |Wählen Sie in der Dropdownliste Ihre vorhandene Ressourcengruppe oder die Option **Neu erstellen** aus.|
|Name des Automation-Kontos|Erforderlich |Geben Sie einen eindeutigen Namen für den Standort und die Ressourcengruppe ein. Namen von gelöschten Automation-Konten sind möglicherweise nicht sofort verfügbar. Nach der Eingabe auf der Benutzeroberfläche kann der Kontoname nicht mehr geändert werden. |
|Region|Erforderlich |Wählen Sie aus der Dropdownliste eine Region für Ihr Konto aus. Eine aktualisierte Liste mit Standorten, an denen Sie ein Automation-Konto bereitstellen können, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).|

Das folgende Bild zeigt eine Standardkonfiguration für ein neues Automation-Konto.

:::image type="content" source="./media/automation-create-standalone-account/create-account-basics.png" alt-text="Zum Erstellen eines Automation-Kontos erforderliche Felder auf der Registerkarte „Grundlagen“":::

### <a name="advanced"></a>Fortgeschrittene

Auf der Registerkarte **Erweitert** können Sie die Option für die verwaltete Identität für Ihr neues Automation-Konto konfigurieren. Die Option für die benutzerseitig zugewiesene verwaltete Identität kann auch konfiguriert werden, nachdem das Automation-Konto erstellt wurde.

Anweisungen zum Erstellen einer benutzerseitig zugewiesenen verwalteten Identität finden Sie unter [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

In der folgenden Tabelle werden die Felder der Registerkarte **Erweitert** beschrieben.

| **Feld** | **Erforderlich**<br> **or**<br> **optional** |**Beschreibung** |
|---|---|---|
|Systemseitig zugewiesen |Optional |Eine Azure Active Directory-Identität, die an den Lebenszyklus des Automation-Kontos gebunden ist |
|Benutzerseitig zugewiesen |Optional |Eine verwaltete Identität, die als eigenständige Azure-Ressource dargestellt und getrennt von den Ressourcen verwaltet wird, die sie verwenden|

Sie können verwaltete Identitäten später aktivieren, und das Automation-Konto wird ohne eine solche Identität erstellt. Informationen zum Aktivieren einer verwalteten Identität nach dem Erstellen des Kontos finden Sie unter [Aktivieren einer verwalteten Identität](enable-managed-identity-for-automation.md). Wenn Sie beide Optionen aktivieren, wählen Sie für die benutzerseitig zugewiesene Identität die Option **Add user assigned identities** (Benutzerseitig zugewiesene Identitäten hinzufügen) aus. Wählen Sie auf der Seite **Benutzerseitig zugewiesene verwaltete Identität auswählen** ein Abonnement aus, und fügen Sie mindestens eine in diesem Abonnement erstellte benutzerseitig zugewiesene Identität hinzu, um sie dem Automation-Konto zuzuweisen.

Das folgende Bild zeigt eine Standardkonfiguration für ein neues Automation-Konto.

:::image type="content" source="./media/automation-create-standalone-account/create-account-advanced.png" alt-text="Zum Erstellen des Automation-Kontos erforderliche Felder auf der Registerkarte „Erweitert“":::

### <a name="tags-tab"></a>Registerkarte „Tags“

Auf der Registerkarte **Tags** können Sie Resource Manager-Tags angeben, um Ihre Azure-Ressourcen zu organisieren. Weitere Informationen finden Sie unter [Markieren von Ressourcen, Ressourcengruppen und Abonnements für die logische Organisation](../azure-resource-manager/management/tag-resources.md).

### <a name="review--create-tab"></a>Registerkarte „Überprüfen und erstellen“

Wenn Sie zur Registerkarte **Überprüfen + erstellen** wechseln, führt Azure die Überprüfung der ausgewählten Automation-Kontoeinstellungen aus. Wenn die Überprüfung erfolgreich ist, können Sie mit dem Erstellen des Automation-Kontos fortfahren.

Wenn die Überprüfung fehlschlägt, gibt das Portal an, welche Einstellungen geändert werden müssen.

Überprüfen Sie Ihr neues Automation-Konto.

:::image type="content" source="./media/automation-create-standalone-account/automation-account-overview.png" alt-text="Übersichtsseite für das Automation-Konto":::

Nach der erfolgreichen Erstellung des Automation-Kontos werden automatisch verschiedene Ressourcen erstellt. Nachdem diese Ressourcen erstellt sind, können Runbooks gefahrlos gelöscht, wenn Sie diese nicht behalten möchten. Die verwalteten Identitäten können für die Authentifizierung bei Ihrem Konto in einem Runbook verwendet werden und sollten erhalten bleiben, es sei denn, Sie erstellen eine andere Identität oder Sie benötigen sie nicht. In der folgenden Tabelle sind die Ressourcen für das Konto zusammengefasst.

|Ressource | Beschreibung | |------||------| |AzureAutomationTutorial-Runbook | Ein grafisches Beispielrunbook, das veranschaulicht, wie die Authentifizierung mithilfe eines ausführenden Kontos funktioniert. Das Runbook ruft alle Resource Manager-Ressourcen ab. | |AzureAutomationTutorialScript |Ein PowerShell-Beispielrunbook, das veranschaulicht, wie die Authentifizierung mithilfe eines ausführenden Kontos funktioniert. Das Runbook erhält alle Resource Manager-Ressourcen.| |AzureAutomationTutorialPython2Runbook |Ein Python-Beispielrunbook, das veranschaulicht, wie die Authentifizierung mithilfe eines ausführenden Kontos funktioniert. Das Runbook listet alle im Abonnement vorhandenen Ressourcengruppen auf.|

> [!NOTE]
> Die Tutorialrunbooks wurden nicht aktualisiert, um sich mit einer verwalteten Identität zu authentifizieren. Lesen Sie [Verwenden einer systemseitig zugewiesenen Identität](enable-managed-identity-for-automation.md#give-access-to-azure-resources-by-obtaining-a-token) oder [Verwenden einer benutzerseitig zugewiesenen Identität](add-user-assigned-identity.md#give-identity-access-to-azure-resources-by-obtaining-a-token), um zu erfahren, wie Sie der verwalteten Identität Zugriff auf Ressourcen gewähren und Ihre Runbooks für die Authentifizierung mit einem der beiden Typen von verwalteten Identitäten konfigurieren können.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den ersten Schritten mit PowerShell-Runbooks finden Sie unter [Tutorial: Erstellen eines PowerShell-Runbooks](./learn/powershell-runbook-managed-identity.md).
* Informationen zu den ersten Schritten mit PowerShell-Workflow-Runbooks finden Sie unter [Tutorial: Erstellen eines PowerShell-Workflow-Runbooks](learn/automation-tutorial-runbook-textual.md).
* Informationen zu den ersten Schritten mit Python 3-Runbooks finden Sie unter [Tutorial: Erstellen eines Python 3-Runbooks](learn/automation-tutorial-runbook-textual-python-3.md).
* Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation).
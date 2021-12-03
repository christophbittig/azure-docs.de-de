---
title: Aktualisieren von Azure PowerShell-Modulen in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie häufig verwendete Azure PowerShell-Module aktualisieren, die standardmäßig in Azure Automation bereitgestellt werden.
services: automation
ms.subservice: process-automation
ms.date: 09/24/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4344d8c12c139c428f44d417ccde2c5e950ed307
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477161"
---
# <a name="update-azure-powershell-modules-in-automation"></a>Azure PowerShell-Module in der Automatisierung aktualisieren

Die am häufigsten verwendeten PowerShell-Module werden standardmäßig in jedem Automation-Konto bereitgestellt. Weitere Informationen finden Sie unter [Standardmodule](shared-resources/modules.md#default-modules). Da das Azure-Team die Azure-Module regelmäßig aktualisiert, kann es bei den enthaltenen Cmdlets zu Änderungen kommen. Diese Änderungen, z. B. das Umbenennen eines Parameters oder das vollständige Einstellen eines Cmdlets, können nachteilige Auswirkungen auf Ihre Runbooks haben. 

> [!NOTE]
> Globale Module (also Module, die standardmäßig von Automation bereitgestellt werden) können nicht gelöscht werden.

## <a name="set-up-an-automation-account"></a>Einrichten eines Automation-Kontos

Führen Sie im Zuge von Aktualisierungen Tests und Überprüfungen durch, um Auswirkungen auf Ihre Runbooks und die damit automatisierten Prozesse zu vermeiden. Wenn Sie für diesen Zweck kein dediziertes Automation-Konto haben, sollten Sie eines erstellen, damit Sie während der Entwicklung Ihrer Runbooks viele verschiedene Szenarios testen können. Diese Tests sollten iterative Änderungen wie das Aktualisieren der PowerShell-Module einschließen.

Stellen Sie sicher, dass Ihrem Automation-Konto eine [systemseitig zugewiesene verwaltete Identität oder eine benutzerseitig zugewiesene verwaltete Identität](quickstarts/enable-managed-identity.md) hinzugefügt wurde.

Wenn Sie Ihre Skripts lokal entwickeln, empfiehlt es sich, beim Testen lokal die gleichen Modulversionen zu verwenden wie in Ihrem Automation-Konto, um sicherzustellen, dass Sie die gleichen Ergebnisse erhalten. Nach dem Überprüfen der Ergebnisse und dem Anwenden aller erforderlichen Änderungen können Sie die Änderungen in die Produktion übernehmen.

> [!NOTE]
> Ein neues Automation-Konto enthält ggf. nicht die neuesten Module.

## <a name="update-az-modules"></a>Az-Module aktualisieren

Derzeit ist die Aktualisierung von AZ-Modulen nur über das Portal möglich. Aktualisierungen über PowerShell und ARM-Vorlage werden in Zukunft verfügbar sein. Nur Standard-AZ-Module werden aktualisiert, wenn Sie die folgenden Schritte ausführen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem Automation-Konto.
1. Wählen Sie unter  **Freigegebene Ressourcen** die Option  **Module** aus.
1. Wählen Sie **Az-Module aktualisieren**.
1. Wählen Sie **Modul zum Aktualisieren**. Standardmäßig wird das Az-Modul angezeigt.  
1. Wählen Sie in der Dropdownliste **Modulversion** und **Runtimeversion** aus.
1. Wählen Sie **Aktualisieren**, um das Az-Modul auf die von Ihnen gewählte Version zu aktualisieren.
   Auf der Seite **Module** können Sie die Liste wie unten dargestellt anzeigen:

   :::image type="content" source="./media/automation-update-azure-modules/update-az-modules-portal.png" alt-text="Aktualisierung der AZ-Modulseite mit Auswahlen.":::

Wenn Sie eine niedrigere Version als die im Automatisierungskonto importierte Version des Az-Moduls auswählen, führt der Aktualisierungsvorgang ein Rollback auf die ausgewählte niedrigere Version durch.  

Sie können den Aktualisierungsvorgang überprüfen, indem Sie die Eigenschaften Modulversion und Status der aktualisierten Module überprüfen, die in der Liste der **Module** unter **PowerShell-Module** angezeigt werden. 

Das Azure-Team wird die Modulversion regelmäßig aktualisieren und eine Option zur Aktualisierung der **Standard** Az-Module zu aktualisieren, indem Sie die Modulversion in der Dropdown-Liste auswählen.  

## <a name="obtain-a-runbook-to-use-for-updates"></a>Abrufen eines Runbooks für Aktualisierungen

Die Azure-Module in Ihrem Automation-Konto müssen mithilfe des Open-Source-Runbooks [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) aktualisiert werden. Laden Sie dieses Runbook aus dem GitHub-Repository herunter, um es zum Aktualisieren Ihrer Azure-Module zu verwenden. Anschließend können Sie es in Ihr Automation-Konto importieren oder es als Skript ausführen. Informationen zum Importieren eines Runbooks in Ihr Automation-Konto finden Sie unter [Importieren eines Runbooks](manage-runbooks.md#import-a-runbook).

Vom Runbook **Update-AutomationAzureModulesForAccount** wird standardmäßig die Aktualisierung von Azure-, AzureRM- und Az-Modulen unterstützt. Weitere Informationen zum Aktualisieren von Az.Automation-Modulen mit diesem Runbook finden Sie in der [README-Datei zum Runbook zum Aktualisieren von Azure-Modulen](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md). Es gibt weitere wichtige Faktoren, die Sie bei der Verwendung der Az-Module in Ihrem Automation-Konto berücksichtigen müssen. Weitere Informationen finden Sie unter [Verwalten von Modulen in Azure Automation](shared-resources/modules.md).

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Verwenden von Aktualisierungsrunbookcode als reguläres PowerShell-Skript

Der Runbookcode kann als reguläres PowerShell-Skript verwendet werden (anstelle eines Runbooks). Melden Sie sich hierzu zunächst mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Azure an, und übergeben Sie dann `-Login $false` an das Skript.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Verwenden des Aktualisierungsrunbooks für Sovereign Clouds

Wenn Sie dieses Runbook für Sovereign Clouds verwenden möchten, übergeben Sie mithilfe des Parameters `AzEnvironment` die korrekte Umgebung an das Runbook. Zulässige Werte sind „AzureCloud“ (öffentliche Azure-Cloud), „AzureChinaCloud“, „AzureGermanCloud“ und „AzureUSGovernment“. Diese Werte können mithilfe von `Get-AzEnvironment | select Name` abgerufen werden. Wenn Sie keinen Wert an dieses Cmdlet übergeben, wird das Runbook standardmäßig auf AzureCloud festgelegt.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Aktualisieren einer bestimmten Modulversion mithilfe des Aktualisierungsrunbooks

Falls Sie eine bestimmte Azure PowerShell-Modulversion anstelle des neuesten im PowerShell-Katalog verfügbaren Moduls verwenden möchten, übergeben Sie diese Versionen im optionalen Parameter `ModuleVersionOverrides` des **Update-AutomationAzureModulesForAccount**-Runbooks. Beispiele finden Sie im [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1)-Runbook. Azure PowerShell-Module, die im Parameter `ModuleVersionOverrides` nicht erwähnt sind, werden mit den aktuellen Modulversionen aus dem PowerShell-Katalog aktualisiert. Wenn Sie keine Argumente an den Parameter `ModuleVersionOverrides` übergeben, werden alle Module mit den aktuellen Modulversionen aus dem PowerShell-Katalog aktualisiert. Dieses Verhalten entspricht dem der Schaltfläche **Azure-Module aktualisieren** im Azure-Portal.

## <a name="next-steps"></a>Nächste Schritte

* Ausführliche Informationen zur Verwendung von Modulen finden Sie unter [Verwalten von Modulen in Azure Automation](shared-resources/modules.md).
* Informationen zum Aktualisierungsrunbook finden Sie unter [Aktualisieren von Azure PowerShell-Modulen in Azure Automation-Konten](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).

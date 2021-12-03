---
title: Verwenden von Azure Automation-Runbooks und -Modulen im PowerShell-Katalog
description: In diesem Artikel wird erläutert, wie Sie Runbooks und Module aus Microsoft GitHub-Repositorys und dem PowerShell-Katalog verwenden.
services: automation
ms.subservice: process-automation
ms.date: 10/29/2021
ms.topic: conceptual
ms.openlocfilehash: 1f6b3a47d7d8bcab77c80b71fcbc06c3381509c9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131504208"
---
# <a name="use-existing-runbooks-and-modules"></a>Verwenden vorhandener Runbooks und Module

Statt eigene Runbooks und Module in Azure Automation zu erstellen, können Sie Szenarien nutzen, die bereits von Microsoft und der Community entwickelt wurden. Aus dem Runbookkatalog im Azure-Portal können Sie Azure-bezogene PowerShell- und Python-Runbooks abrufen und aus dem PowerShell-Katalog [Module](#modules-in-the-powershell-gallery) und [Runbooks](#runbooks-in-the-powershell-gallery) (die teilweise Azure-spezifisch sein können). Sie können auch etwas zur Community beitragen, indem Sie [von Ihnen entwickelte Szenarien](#contribute-to-the-community) zur Verfügung stellen.

> [!NOTE]
> Das TechNet Script Center (TechNet-Skriptcenter) wird eingestellt. Alle Runbooks aus dem Script Center im Runbookkatalog wurden in unsere [Automation-GitHub-Organisation](https://github.com/azureautomation) verschoben. Weitere Informationen finden Sie unter [Migration der Azure Automation-Runbooks zu GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>Importieren von Runbooks aus GitHub über das Azure-Portal

> [!NOTE]
>- Die Option **Katalog durchsuchen** im Azure-Portal verfügt über eine verbesserte Benutzeroberfläche.
>- Auf dem Blatt **Prozessautomatisierung** > **Runbook** können Sie Runbooks entweder über die Option **Runbook importieren** oder **Katalog durchsuchen** importieren. Auf der Seite **Runbooks** werden zwei neue Spalten angezeigt: **Runtimeversion** und **Runbooktyp**.
 
1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.
1. Wählen Sie das Blatt **Runbooks** unter **Prozessautomatisierung** aus.
1. Klicken Sie auf der Seite **Runbooks** auf **Runbook importieren**.

   :::image type="content" source="./media/automation-runbook-gallery/import-runbook.png" alt-text="Screenshot: Auswahl eines Runbooks über die Option „Runbook importieren“":::

1. Auf der Seite **Runbook importieren** können Sie entweder eine Datei, die auf Ihrem lokalen Computer gespeichert ist, oder eine Datei von GitHub importieren, indem Sie **Nach Datei suchen** oder **Aus Katalog durchsuchen** verwenden.
1. Wählen Sie die Datei aus.
1. Geben Sie den **Namen**, die **Runtimeversion** und die **Beschreibung** ein.
1. Klicken Sie auf **Importieren**.
   
   :::image type="content" source="./media/automation-runbook-gallery/import-runbook-upload-runbook-file.png" alt-text="Screenshot: Auswählen eines Runbooks aus einer Datei oder einem Katalog":::

1. Wählen Sie alternativ auf der Seite **Runbooks** die Option **Katalog durchsuchen** aus, um die verfügbaren Runbooks zu durchsuchen.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-option.png" alt-text="Screenshot: Auswählen der Option „Katalog durchsuchen“ auf dem Blatt „Runbook“":::

1. Sie können die Filter über der Liste verwenden, um die Anzeige nach Herausgeber, Typ und Sortierung einzuschränken. Suchen Sie das gewünschte Katalogelement, und wählen Sie es zum Anzeigen der Details aus.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="Durchsuchen des Runbookkatalogs" lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

1. Klicken Sie auf **Auswählen**, um ein gewähltes Runbook auszuwählen. 
1. Geben Sie auf der Seite **Runbook importieren** den **Namen** ein, und wählen Sie die **Runtimeversionen** aus.
1. Der **Runbooktyp** und die **Beschreibung** werden automatisch ausgefüllt.
1. Klicken Sie auf **Importieren**.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="Importieren eines Katalogelements":::

7. Das Runbook wird auf der Registerkarte **Runbooks** des Automation-Kontos angezeigt.

 
## <a name="runbooks-in-the-powershell-gallery"></a>Runbooks im PowerShell-Katalog

> [!IMPORTANT]
> Sie sollten die Inhalte jedes Runbooks überprüfen, das Sie aus dem PowerShell-Katalog enthalten. Gehen Sie äußerst vorsichtig vor, wenn Sie die Inhalte in einer Produktionsumgebung installieren und ausführen.

Der [PowerShell-Katalog](https://www.powershellgallery.com/packages) bietet verschiedene Runbooks von Microsoft und der Community, die Sie in Azure Automation importieren können. Um eins zu verwenden, können Sie ein Runbook aus dem Katalog herunterladen, oder Sie können Runbooks direkt aus dem Katalog oder aus Ihrem Automation-Konto im Azure-Portal importieren.

> [!NOTE]
> Grafische Runbooks werden im PowerShell-Katalog nicht unterstützt.

Das direkte Importieren aus dem PowerShell-Katalog ist nur über das Azure-Portal möglich. Diese Funktion kann nicht mithilfe von PowerShell ausgeführt werden. Das Verfahren ist identisch mit dem unter [Importieren von Runbooks aus GitHub mit dem Azure-Portal](#import-runbooks-from-github-with-the-azure-portal) gezeigten Verfahren, mit der Ausnahme, dass die **Quelle** der **PowerShell-Katalog** ist.

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="Anzeigen der Auswahl der Quelle des Runbookkatalogs" lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>Module im PowerShell-Katalog

PowerShell-Module enthalten Cmdlets, die Sie in Ihren Runbooks verwenden können. Vorhandene Module, die Sie in Azure Automation installieren können, sind im [PowerShell-Katalog](https://www.powershellgallery.com) verfügbar. Sie starten diesen Katalog über das Azure-Portal und installieren die Module direkt in Azure Automation. Sie können sie auch manuell herunterladen und installieren.

Sie finden auch Module, die Sie im Azure-Portal importieren können. Diese finden Sie für Ihr Automation-Konto in den **Modulen** unter **Freigegebene Ressourcen**.

> [!IMPORTANT] 
> Schließen Sie das Schlüsselwort „AzureRm“ in kein Skript ein, das mit dem Az-Modul ausgeführt werden soll. Die Einbeziehung des Schlüsselworts, auch in einen Kommentar, kann dazu führen, dass AzureRm geladen wird und dann in Konflikt mit dem Az-Modul gerät.

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>Allgemeine im PowerShell-Katalog verfügbare Szenarios

Die nachstehende Liste enthält einige Runbooks, die gängige Szenarien unterstützen. Eine vollständige Liste der vom Azure Automation-Team erstellten Runbooks finden Sie im [AzureAutomationTeam-Profil](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion:](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) importiert die neueste Version aller Module aus dem PowerShell-Katalog in ein Automation-Konto.
   * [Enable-AzureDiagnostics:](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) konfiguriert Azure-Diagnose und Log Analytics, sodass Azure Automation-Protokolle mit Auftragsstatus- und Auftragsdatenströmen empfangen werden.
   * [Copy-ItemFromAzureVM:](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) kopiert eine Remotedatei von einem virtuellen Microsoft Azure-Computer.
   * [Copy-ItemToAzureVM:](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) kopiert eine lokale Datei auf einen virtuellen Azure-Computer.

## <a name="contribute-to-the-community"></a>Mitwirken in der Community

Es wird jedem empfohlen, sich in der Azure Automation-Community zu beteiligen, damit diese noch größer wird. Teilen Sie die beeindruckenden Runbooks, die Sie für die Community erstellt haben. Ihre Beiträge werden sehr geschätzt.

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>Hinzufügen eines Runbooks zum Runbookkatalog von GitHub

Mit diesem GitHub-Workflow können Sie dem Runbookkatalog neue PowerShell- oder Python-Runbooks hinzufügen.

1. Erstellen Sie ein öffentliches Repository auf GitHub, und fügen Sie das Runbook und alle anderen erforderlichen Dateien hinzu (z. B. „readme.md“, Beschreibung usw.).
1. Fügen Sie das Thema `azureautomationrunbookgallery` hinzu, um sicherzustellen, dass das Repository von unserem Dienst ermittelt werden kann, und damit es im Automation Runbookkatalog angezeigt werden kann.
1. Wenn das von Ihnen erstellte Runbook ein PowerShell-Workflow ist, fügen Sie das Thema `PowerShellWorkflow` hinzu. Wenn es sich um ein Python 3-Runbook handelt, fügen Sie `Python3` hinzu. Für Azure-Runbooks sind keine weiteren spezifischen Themen erforderlich. Wir empfehlen Ihnen jedoch, weitere Themen hinzuzufügen, die für die Kategorisierung und Suche im Runbookkatalog verwendet werden können.

   >[!NOTE]
   >Sehen Sie sich vorhandene Runbooks im Katalog an, um Informationen zur Formatierung, zu Headern und vorhandenen Tags zu erhalten, die Sie möglicherweise verwenden (z. B. `Azure Automation` oder `Linux Azure Virtual Machines`).

Wenn Sie Änderungen an einem vorhandenen Runbook vorschlagen möchten, reichen Sie einen Pull Request dafür ein. 

Wenn Sie ein vorhandenes Runbook klonen und bearbeiten möchten, sollten Sie ihm einen anderen Namen geben. Wenn Sie den alten Namen erneut verwenden, wird er zweimal in der Runbookkatalogauflistung angezeigt.

>[!NOTE]
>Warten Sie mindestens 12 Stunden für die Synchronisierung zwischen GitHub und dem Automation-Runbookkatalog, sowohl für aktualisierte als auch für neue Runbooks.

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>Hinzufügen eines PowerShell-Runbooks zum PowerShell-Katalog

Microsoft empfiehlt, Runbooks aus dem PowerShell-Katalog hinzuzufügen, die für andere Kunden nützlich sein könnten. Der PowerShell-Katalog akzeptiert PowerShell-Module und PowerShell-Skripts. Sie können ein Runbook hinzufügen, indem Sie [es in den PowerShell-Katalog hochladen](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>Importieren eines Moduls aus dem Modulkatalog im Azure-Portal

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto.
1. Wählen Sie unter **Freigegebene Ressourcen** die Option **Module** aus.
1. Wählen Sie auf der Seite **Module** die Option **Katalog durchsuchen** aus, um die Liste der Module zu öffnen.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="Ansicht des Modulkatalogs." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. Auf der Seite „Katalog durchsuchen“ können Sie anhand der folgenden Felder suchen:

   * Name des Moduls
   * `Tags`
   * Autor
   * Cmdlet-/DSC-Ressourcenname

1. Suchen Sie das gewünschte Modul, und wählen Sie es aus, um seine Details anzuzeigen.

   Wenn Sie einen Drilldown in einem bestimmten Modul ausführen, können Sie weitere Informationen anzeigen. Diese Informationen umfassen einen Link zurück zum PowerShell-Katalog, alle erforderlichen Abhängigkeiten und alle Cmdlets oder DSC-Ressourcen, die das Modul enthält.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="Detaillierte Ansicht eines Moduls aus dem Katalog." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. Um das Modul direkt in Azure Automation zu installieren, klicken Sie auf **Importieren**.
1. Im Bereich „Importieren“ sehen Sie den Namen des zu importierenden Moduls. Wenn alle Abhängigkeiten installiert sind, ist die Schaltfläche **OK** aktiv. Falls Abhängigkeiten fehlen, müssen diese Abhängigkeiten importiert werden, bevor dieses Modul importiert werden kann.
1. Klicken Sie im Bereich „Importieren“ auf **OK**, um das Modul zu importieren. Wenn Azure Automation ein Modul in Ihr Konto importiert, werden Metadaten zum Modul und den Cmdlets extrahiert. Dieser Vorgang kann einige Minuten dauern, da jede Aktivität extrahiert werden muss.
1. Sie erhalten jeweils eine Benachrichtigung, wenn das Modul bereitgestellt wird und wenn der Vorgang abgeschlossen ist.
1. Nachdem das Modul importiert wurde, können Sie die verfügbaren Aktivitäten anzeigen. Sie können Modulressourcen in Ihren Runbooks und DSC-Ressourcen verwenden.

> [!NOTE]
> Module, die nur PowerShell Core unterstützen, werden in Azure Automation nicht unterstützt und können nicht in das Azure-Portal importiert werden oder direkt über den PowerShell-Katalog bereitgestellt werden.

## <a name="request-a-runbook-or-module"></a>Anfordern eines Runbooks oder Moduls

Sie können Anforderungen an [User Voice](https://feedback.azure.com/d365community/forum/8ddd03a2-0225-ec11-b6e6-000d3a4f0858)senden.  Wenn Sie Hilfe beim Schreiben eines Runbooks benötigen oder eine Frage zu PowerShell haben, stellen Sie eine Frage auf der [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den ersten Schritten mit PowerShell-Runbooks finden Sie unter [Tutorial: Erstellen eines PowerShell-Runbooks](./learn/powershell-runbook-managed-identity.md).
* Informationen zur Arbeit mit Runbooks finden Sie unter [Verwalten eines Runbooks in Azure Automation](manage-runbooks.md).
* Weitere Informationen zur PowerShell-Skripterstellung finden Sie in der [PowerShell-Dokumentation](/powershell/scripting/overview).
* Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation).

---
title: Hotpatch für Windows Server Azure Edition (Vorschau)
description: Erfahren Sie, wie Hotpatch für Windows Server Azure Edition funktioniert und wie Sie es aktivieren.
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4794813af900df66fd2d7cd6fb8e238f79d18d4b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327593"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Hotpatch für neue VMs (Vorschau)

> [!IMPORTANT]
> Der Hotpatch ist zurzeit in öffentlicher Vorschauversion verfügbar. Es ist ein Anmeldungsverfahren (opt-in) erforderlich, um die unten beschriebene Hotpatch-Funktion verwenden zu können.
> Diese Vorschau wird ohne Vereinbarung zum Servicelevel bereitgestellt und nicht für Produktionsworkloads empfohlen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Hotpatching ist eine neue Methode zur Installation von Updates auf unterstützten virtuellen Computern (VMs) der _Windows Server Azure Edition_, die keinen Neustart nach der Installation erfordert. Dieser Artikel enthält Informationen über Hotpatches für unterstützte _Windows Server Azure Edition_-VMs, die folgende Vorteile bieten:
* Geringere Auswirkungen auf die Workload mit weniger Neustarts
* Schnellere Bereitstellung von Updates, da die Pakete kleiner sind, schneller installiert werden und eine einfachere Patchorchestrierung mit Azure Update Manager möglich ist
* Besserer Schutz, da die Hotpatch-Updatepakete auf Windows-Sicherheitsupdates beschränkt sind, die schneller und ohne Neustart installiert werden

## <a name="how-hotpatch-works"></a>Funktionsweise von Hotpatches

Hotpatch funktioniert, indem zuerst eine Baseline mit einem aktuellen kumulativen Update von Windows Update festgelegt wird. Es werden regelmäßig Hotpatches freigegeben (z. B. am zweiten Dienstag des Monats), die auf dieser Baseline aufsetzen. Hotpatches enthalten Updates, für die kein Neustart erforderlich ist. Die Baseline wird in regelmäßigen Abständen (beginnend mit „alle drei Monate“) mit einem neuen aktuellen kumulativen Update aktualisiert.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch-Beispielzeitplan":::

Es gibt zwei Arten von Baselines: **Geplante Baselines** und **Nicht geplante Baselines**.
*  **Geplante Baselines** werden in einem regelmäßigen Rhythmus veröffentlicht, wobei zwischendurch Hotpatch-Releases veröffentlicht werden.  Geplante Baselines enthalten alle Updates in einem vergleichbaren _letzten kumulativen Update_ für diesen Monat und erfordern einen Neustart.
    * Der obige Beispielplan zeigt vier geplante Baselinereleases in einem Kalenderjahr (insgesamt fünf im Diagramm) und acht Hotpatch-Releases.
* **Nicht geplante Baselines** werden freigegeben, wenn ein wichtiges Update (z. B. ein Zero-Day-Fix) veröffentlicht wird und dieses spezielle Update nicht als Hotpatch freigegeben werden kann.  Wenn nicht geplante Baselines veröffentlicht werden, wird ein Hotpatch-Release in diesem Monat durch eine nicht geplante Baseline ersetzt.  Nicht geplante Baselines umfassen auch alle Updates in einem vergleichbaren _letzten kumulativen Update_ für diesen Monat und erfordern ebenfalls einen Neustart.
    * Der obige Beispielzeitplan zeigt zwei nicht geplante Baselines, die die Hotpatch-Releases für diese Monate ersetzen würden (die tatsächliche Anzahl nicht geplanter Baselines in einem Jahr ist im Voraus nicht bekannt).

## <a name="regional-availability"></a>Regionale Verfügbarkeit
Hotpatch ist in allen globalen Azure-Regionen als Vorschauversion verfügbar. Azure Government-Regionen werden in der Vorschauversion nicht unterstützt.

## <a name="how-to-get-started"></a>Erste Schritte

> [!NOTE]
> Während der Vorschauphase können Sie über [diesen Link](https://aka.ms/ws2022ae-portal-preview) im Azure-Portal beginnen.

Gehen Sie folgendermaßen vor, um Hotpatch auf einer neuen VM zu verwenden:
1.  Aktivieren des Zugriffs auf die Vorschauversion
    * Pro Abonnement ist eine einmalige Freigabe des Zugriffs auf die Vorschauversion erforderlich.
    * Der Zugriff auf die Vorschauversion kann über die API, PowerShell oder die Befehlszeilenschnittstelle aktiviert werden, wie im folgenden Abschnitt beschrieben.
1.  Beginnen Sie mit dem Erstellen eines neuen virtuellen Computers über Azure-Portal
    * Während der Vorschau müssen Sie über [diesen Link](https://aka.ms/ws2022ae-portal-preview) starten.
1.  Details während der VM-Erstellung
    * Stellen Sie sicher, dass ein unterstütztes _Windows Server 2019 Datacenter: Azure Edition_ Bild in der Dropdownliste für Bilder ausgewählt ist.  Verwenden [Sie diesen Leitfaden](automanage-windows-server-services-overview.md#getting-started-with-windows-server-azure-edition), um zu bestimmen, welche Bilder unterstützt werden.
    * Aktivieren Sie auf der Registerkarte Verwaltung im Abschnitt "Gastbetriebssystemupdates" das Kontrollkästchen "Hotpatch aktivieren", um Hotpatch in der Vorschau zu bewerten.  Die Optionen für die Patch-Instrumentierung werden auf "Azure-orchestriert" festgelegt. 
    * Wählen Sie auf der Registerkarte Verwaltung im Abschnitt "Azure Automanage" für "Azure Automanage-Umgebung" die Option "Dev/Test" oder "Produktion" aus, um die bewährten Methoden für die automatische Verwaltung von Computern in der Vorschauversion zu bewerten.
    
1. Erstellen einer neuen VM

## <a name="enabling-preview-access"></a>Aktivieren des Vorschauzugriffs

### <a name="rest-api"></a>REST-API

Im folgenden Beispiel wird beschrieben, wie Sie die Vorschauversion für Ihr Abonnement aktivieren:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

Die Featureregistrierung kann bis zu 15 Minuten dauern. So überprüfen Sie den Registrierungsstatus:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

Schließen Sie den Opt-in-Prozess ab, nachdem Sie das Feature für Ihr Abonnement registriert haben, indem Sie die Änderung an den Computeressourcenanbieter weitergeben.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

Verwenden Sie das Cmdlet ```Register-AzProviderFeature```, um die Vorschauversion für Ihr Abonnement zu aktivieren.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Die Featureregistrierung kann bis zu 15 Minuten dauern. So überprüfen Sie den Registrierungsstatus:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Schließen Sie den Opt-in-Prozess ab, nachdem Sie das Feature für Ihr Abonnement registriert haben, indem Sie die Änderung an den Computeressourcenanbieter weitergeben.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

Verwenden Sie ```az feature register```, um die Vorschauversion für Ihr Abonnement zu aktivieren.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Die Featureregistrierung kann bis zu 15 Minuten dauern. So überprüfen Sie den Registrierungsstatus:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Schließen Sie den Opt-in-Prozess ab, nachdem Sie das Feature für Ihr Abonnement registriert haben, indem Sie die Änderung an den Computeressourcenanbieter weitergeben.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Patchinstallation

Während der Vorschau ist die Funktion der [automatischen VM-Gastpatches](../virtual-machines/automatic-vm-guest-patching.md) automatisch für alle mit einem unterstützten _Windows Server Azure Edition_-Image erstellten VMs aktiviert. Mit aktivierten automatischen VM-Gastpatches:
* Patches, die als Kritisch oder Sicherheit klassifiziert werden, werden automatisch heruntergeladen und auf die VM angewendet.
* Patches werden außerhalb der Spitzenzeiten in der Zeitzone der VM angewendet.
* Die Patchorchestrierung wird von Azure verwaltet, und Patches werden nach den [verfügbarkeitsbasierten Prinzipien](../virtual-machines/automatic-vm-guest-patching.md#availability-first-updates) angewendet.
* Die Integrität des virtuellen Computers wird anhand von Integritätssignalen der Plattform ermittelt und überwacht, um Patchfehler zu erkennen.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Wie funktioniert automatisches VM-Gastpatchen?

Wenn die Option für [Automatische VM-Gastpatches](../virtual-machines/automatic-vm-guest-patching.md) auf einer VM aktiviert ist, werden die verfügbaren kritischen und Sicherheitspatches heruntergeladen und automatisch angewendet. Dieser Prozess wird jeden Monat automatisch gestartet, wenn neue Patches freigegeben werden. Die Patchbewertung und -installation erfolgt automatisch, und der Vorgang umfasst einen Neustart der VM bei Bedarf.

Wenn die Hotpatchfunktion auf unterstützten _Windows Server Azure Edition_-VMs aktiviert ist, werden die meisten monatlichen Sicherheitsupdates als Hotpatches bereitgestellt, die keinen Neustart erfordern. Aktuelle kumulative Updates, die an Monaten mit geplanten oder nicht geplanten Baselines gesendet werden, erfordern einen Neustart des virtuellen Computers. Zusätzliche kritische oder sicherheitsrelevante Patches können ebenfalls in regelmäßigen Abständen verfügbar sein, was einen Neustart des virtuellen Computers erforderlich machen kann.

Der virtuelle Computer wird automatisch alle paar Tage und mehrmals innerhalb eines 30-Tage-Zeitraums überprüft, um die anwendbaren Patches für diese VM zu ermitteln. Diese automatische Bewertung stellt sicher, dass alle fehlenden Patches zum frühestmöglichen Zeitpunkt erkannt werden.

Patches werden innerhalb von 30 Tagen nach den monatlichen Patchreleases installiert, wobei die [verfügbarkeitsbasierten Prinzipien](../virtual-machines/automatic-vm-guest-patching.md#availability-first-updates) gelten. Patches werden nur außerhalb der Spitzenzeiten für die VM installiert, abhängig von der Zeitzone der VM. Die VM muss außerhalb der Spitzenzeiten ausgeführt werden, damit Patches automatisch installiert werden können. Wenn eine VM während einer periodischen Bewertung ausgeschaltet wird, wird die VM bewertet, und die anwendbaren Patches werden bei der nächsten periodischen Bewertung automatisch installiert, wenn die VM eingeschaltet wird. Die nächste regelmäßige Bewertung erfolgt normalerweise innerhalb weniger Tage.

Definitionsupdates und andere Patches, die nicht als „Kritisch“ oder „Sicherheit“ eingestuft sind, werden nicht über automatische VM-Gastpatches installiert.

## <a name="understanding-the-patch-status-for-your-vm"></a>Grundlegendes zum Patchstatus für Ihre VM

Um den Patchstatus für Ihre VM anzuzeigen, navigieren Sie im Azure-Portal zum Abschnitt **Gast- und Hostupdates** für Ihre VM. Klicken Sie unter dem Abschnitt **Updates für das Gastbetriebssystem** auf „Zu Hotpatch wechseln (Vorschau)“, um den neuesten Patchstatus für Ihre VM anzuzeigen.

Auf diesem Bildschirm wird der Hotpatch-Status für Ihre VM angezeigt. Sie können auch überprüfen, ob es verfügbare Patches für Ihre VM gibt, die noch nicht installiert wurden. Wie im obigen Abschnitt zur Patchinstallation beschrieben, werden alle sicherheitsbezogenen und kritischen Updates mithilfe [automatischer VM-Gastpatches](../virtual-machines/automatic-vm-guest-patching.md) automatisch auf Ihrer VM installiert, ohne dass zusätzliche Aktionen erforderlich sind. Patches mit anderen Updateklassifizierungen werden nicht automatisch installiert. Stattdessen werden sie in der Liste der verfügbaren Patches auf der Registerkarte „Updatekonformität“ angezeigt. Sie können auch den Verlauf der Bereitstellung von Updates auf Ihrer VM über den „Updateverlauf“ anzeigen. Der Updateverlauf der letzten 30 Tage wird zusammen mit den Details zur Patchinstallation angezeigt.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Hotpatch-Verwaltung":::

Mit den automatischen VM-Gastpatches wird Ihre VM regelmäßig und automatisch auf verfügbare Updates geprüft. Diese regelmäßigen Bewertungen stellen sicher, dass verfügbare Patches erkannt werden. Sie können die Ergebnisse der Bewertung auf dem Bildschirm „Updates“ oben anzeigen, einschließlich des Zeitpunkts der letzten Bewertung. Sie können auch jederzeit eine bedarfsgesteuerte Patchbewertung für Ihre VM mithilfe der Option „Jetzt bewerten“ auslösen und die Ergebnisse nach Abschluss der Bewertung überprüfen.

Ähnlich wie bei der bedarfsgesteuerten Patchbewertung können Sie auch Patches bedarfsgesteuert für Ihre VM installieren, indem Sie die Option „Updates jetzt installieren“ verwenden. Hier können Sie auswählen, ob alle Updates unter bestimmten Patchklassifizierungen installiert werden sollen. Sie können auch angeben, welche Updates ein- oder ausgeschlossen werden sollen, indem Sie eine Liste mit einzelnen Artikeln der Wissensdatenbank bereitstellen. Patches, die bedarfsgesteuert installiert werden, werden nicht mithilfe von verfügbarkeitsbasierten Prinzipien installiert und erfordern möglicherweise mehr Neustarts und Downtime der VM für die Installation des Updates.

## <a name="supported-updates"></a>Unterstützte Updates

Hotpatch deckt Windows-Sicherheitsupdates ab und hält die Parität mit dem Inhalt von Sicherheitsupdates aufrecht, die über den regulären (Nicht-Hotpatch) Windows-Updatekanal ausgegeben werden.

Beim Betrieb einer unterstützten _Windows Server Azure Edition_-VM mit aktivierter Hotpatchfunktion sind einige wichtige Überlegungen zu berücksichtigen. Neustarts sind weiterhin erforderlich, um Updates zu installieren, die nicht im Hotpatch-Programm enthalten sind. Neustarts sind auch regelmäßig erforderlich, nachdem eine neue Baseline installiert wurde. Diese Neustarts halten die VM auf dem neuesten Stand der nicht sicherheitsrelevanten Patches, die im letzten kumulativen Update enthalten sind.
* Zu den Patches, die derzeit nicht im Hotpatch-Programm enthalten sind, gehören nicht sicherheitsrelevante Updates, die für Windows veröffentlicht werden, und Nicht-Windows-Updates (z. B. .NET-Patches).  Diese Arten von Patches müssen während eines Baselinemonats installiert werden und erfordern einen Neustart.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="what-is-hotpatching"></a>Was ist Hotpatching?

* Hotpatching ist eine neue Methode zur Installation von Updates auf einer unterstützten _Windows Server Azure Edition_-VM, die keinen Neustart nach der Installation erfordert. Es funktioniert, indem es den Code im Arbeitsspeicher von aktiven Prozessen patcht, ohne dass ein Neustart des Prozesses erforderlich ist.

### <a name="how-does-hotpatching-work"></a>Wie funktioniert das Hotpatching?

* Das Hotpatching funktioniert, indem eine Baseline mit einem aktuellen kumulativen Windows Update erstellt wird und dann anhand dieser Baseline entsprechende Updates erstellt werden, die keinen Neustart erfordern, um wirksam zu werden.  Die Baseline wird in regelmäßigen Abständen mit einem neuen kumulativen Update aktualisiert. Das kumulative Update enthält alle Sicherheits- und Qualitätsupdates und erfordert einen Neustart.

### <a name="why-should-i-use-hotpatch"></a>Warum sollte ich Hotpatch verwenden?

* Wenn Sie Hotpatch auf einem unterstützten _Windows Server Azure Edition_-Image verwenden, profitiert Ihre VM von einer höheren Verfügbarkeit (weniger Neustarts) und schnelleren Updates (kleinere Pakete, die schneller installiert werden, ohne dass Prozesse neu gestartet werden müssen). Das Ergebnis dieses Prozesses ist eine VM, die immer auf dem neuesten Stand und sicher ist.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Welche Arten von Updates werden von Hotpatch abgedeckt?

* Hotpatch deckt derzeit Windows-Sicherheitsupdates ab.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Wann werde ich das erste Hotpatch-Update erhalten?

* Hotpatch-Updates werden in der Regel am zweiten Dienstag eines jeden Monats veröffentlicht. Weitere Informationen finden Sie unten.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Wie wird der Hotpatch-Zeitplan aussehen?

* Das Hotpatching funktioniert, indem eine Baseline mit einem aktuellen kumulativen Windows Update erstellt wird und dann anhand dieser Baseline Hotpatch-Updates mit monatlicher Veröffentlichung erstellt werden.  Während der Vorschau werden die Baselines zunächst alle drei Monate veröffentlicht. In der Abbildung unten sehen Sie ein Beispiel für einen jährlichen Dreimonatsplan (einschließlich nicht geplanter Baselines aufgrund von Zero-Day-Fixes).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch-Beispielzeitplan":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>Sind für einen in Hotpatch registrierten virtuellen Computer noch Neustarts erforderlich?

* Neustarts sind weiterhin erforderlich, um Updates zu installieren, die nicht im Hotpatch-Programm enthalten sind, und sind regelmäßig erforderlich, nachdem eine Baseline (letztes kumulatives Update von Windows Update) installiert wurde. Durch diesen Neustart wird Ihre VM mit allen Patches, die im kumulativen Update enthalten sind, auf dem neuesten Stand gehalten. Die Baselines (die einen Neustart erfordern) beginnen mit einem dreimonatigen Rhythmus, der mit der Zeit verlängert wird.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Sind meine Anwendungen betroffen, wenn ein Hotpatch-Update installiert wird?

* Da Hotpatch den Code im Arbeitsspeicher von aktiven Prozessen patcht, ohne dass ein Neustart des Prozesses erforderlich ist, sind Ihre Anwendungen durch das Patching nicht beeinträchtigt. Beachten Sie, dass dies unabhängig von möglichen Auswirkungen auf die Leistung und Funktionalität des Patches selbst ist.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Kann ich Hotpatch auf meiner VM deaktivieren?

* Sie können Hotpatch auf einer VM über das Azure-Portal deaktivieren.  Durch das Deaktivieren von Hotpatch wird die Registrierung der VM bei Hotpatch aufgehoben, wodurch die VM zum typischen Updateverhalten für Windows Server zurückkehrt.  Sobald Sie die Registrierung bei Hotpatch auf einer VM aufheben, können Sie diese VM erneut registrieren, wenn die nächste Hotpatch-Baseline veröffentlicht wird.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Kann ich ein Upgrade von meinem bestehenden Windows Server-Betriebssystem durchführen?

* Ja, ein Upgrade von vorhandenen Versionen von Windows Server (d. h. Windows Server 2016 oder Windows Server 2019) auf _Windows Server 2022 Datacenter: Azure Edition_ wird unterstützt. 

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Kann ich Hotpatch für Produktionsworkloads während der Vorschau verwenden?

* Vorschauversionen sind nur für Testzwecke und nicht für die Verwendung in Produktionsumgebungen gedacht.

### <a name="will-i-be-charged-during-the-preview"></a>Werden mir während der Vorschau Gebühren berechnet?

* Die Lizenz für _Windows Server Azure Edition_ ist während der Vorschau kostenlos. Die Kosten für die zugrundeliegende Infrastruktur, die für Ihre VM eingerichtet wurde (Speicher, Compute-Instanz, Netzwerk usw.), werden jedoch weiterhin Ihrem Abonnement in Rechnung gestellt.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Wie erhalte ich Unterstützung bei der Problembehandlung für Hotpatching?

* Sie können ein [Ticket für den technischen Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) einreichen. Für die Option „Service“ suchen Sie unter „Compute“ nach **Virtueller Computer mit Windows** und wählen ihn aus. Wählen Sie **Azure-Features** für den Problemtyp und **Automatische VM-Gastpatches** für den Untertyp des Problems aus.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur [Azure-Updateverwaltung](../automation/update-management/overview.md) finden Sie hier
* Weitere Informationen zu [automatischen VM-Gastpatches](../virtual-machines/automatic-vm-guest-patching.md) finden Sie hier
* Weitere Informationen zu [Automanage für Windows Server](automanage-windows-server-services-overview.md)

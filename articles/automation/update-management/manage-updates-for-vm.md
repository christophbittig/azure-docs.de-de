---
title: Verwalten von Updates und Patches für Ihre VMs in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie die Updateverwaltung verwenden können, um Updates und Patches für Ihre Azure- und Nicht-Azure-VMs zu verwalten.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 08/25/2021
ms.openlocfilehash: 932f5d93c5fa67de486ddb9cabaafd68384f0db8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357473"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Verwalten von Updates und Patches für Ihre VMs

Softwareupdates in der Azure Automation-Updateverwaltung bieten eine Gruppe von Tools und Ressourcen, mit denen die komplexe Aufgabe der Nachverfolgung und Anwendung von Softwareupdates auf Computer in Azure und Hybrid Cloud leichter verwaltet werden kann. Ein effektiver Softwareupdate-Verwaltungsprozess ist erforderlich, um die Betriebseffektivität aufrechtzuerhalten, Sicherheitsprobleme zu beheben und die Risiken gesteigerter Cybersicherheitsbedrohungen zu reduzieren. Da die Technologie sich kontinuierlich wandelt und immer wieder neue Sicherheitsrisiken auftreten, gebührt der effektiven Verwaltung von Softwareupdates besonderes Augenmerk.

> [!NOTE]
> Die Updateverwaltung unterstützt die Bereitstellung von Erstanbieterupdates sowie deren Vorabdownload. Hierzu sind Änderungen an den Systemen erforderlich, die aktualisiert werden. Informationen zum Konfigurieren dieser Einstellungen auf Ihren Systemen finden Sie unter [Konfigurieren von Windows Update-Einstellungen für die Azure Automation-Updateverwaltung](configure-wuagent.md).

Bevor Sie versuchen, Updates für Ihre virtuellen Computer zu verwalten, vergewissern Sie sich, dass Sie die Updateverwaltung mit einer der folgenden Methoden auf ihnen aktiviert haben:

* [Aktivieren der Updateverwaltung über ein Automation-Konto](enable-from-automation-account.md)
* [Aktivieren der Updateverwaltung mittels Durchsuchen im Azure-Portal](enable-from-portal.md)
* [Aktivieren der Updateverwaltung über ein Runbook](enable-from-runbook.md)
* [Aktivieren der Updateverwaltung über einen virtuellen Azure-Computer](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Beschränken des Bereichs für die Bereitstellung

Die Updateverwaltung verwendet eine Bereichskonfiguration innerhalb des Arbeitsbereichs, um die Computer, die Updates erhalten sollen, zu erreichen. Weitere Informationen finden Sie unter [Einschränken des Bereitstellungsbereichs für die Updateverwaltung](scope-configuration.md).

## <a name="compliance-assessment"></a>Kompatibilitätsbewertung

Bevor Sie Softwareupdates auf ihren Computern bereitstellen, überprüfen Sie die Ergebnisse der Updatekompatibilitätsbewertung für aktivierte Computer. Für jedes Softwareupdate wird der zugehörige Kompatibilitätszustand aufgezeichnet, nach Abschluss der Bewertung erfasst und per Massenvorgang an Azure Monitor-Protokolle weitergeleitet.

Auf einem Windows-Computer wird der Konformitätsscan standardmäßig alle 12 Stunden ausgeführt und innerhalb von 15 Minuten nach einem Neustart des Log Analytics-Agents für Windows initiiert. Die Auswertungsdaten werden dann an den Arbeitsbereich weitergeleitet, um die Tabelle **Updates** zu aktualisieren. Vor und nach der Updateinstallation wird ein Konformitätsscan ausgeführt, um fehlende Updates zu ermitteln. Die Ergebnisse werden jedoch nicht zum Aktualisieren der Auswertungsdaten in der Tabelle verwendet.

Lesen Sie unbedingt unsere Empfehlungen zum [Konfigurieren von Windows Update-Einstellungen für die Azure Automation-Updateverwaltung](configure-wuagent.md), um Probleme zu vermeiden, die eine korrekte Verwaltung verhindern.

Für einen Linux-Computer wird der Konformitätsscan standardmäßig jede Stunde durchgeführt. Wenn der Log Analytics-Agent für Linux neu gestartet wird, wird ein Konformitätsscan innerhalb von 15 Minuten eingeleitet.

Die Kompatibilitätsergebnisse werden für jeden bewerteten Computer in der Updateverwaltung angezeigt. Bis für einen neu für die Verwaltung aktivierten Computer aktualisierte Daten im Dashboard angezeigt werden, können bis zu 30 Minuten vergehen.

Weitere Informationen zum Anzeigen von Kompatibilitätsergebnissen finden Sie unter [Anzeigen von Updatebewertungen](view-update-assessments.md).

## <a name="deploy-updates"></a>Bereitstellen von Updates

Nach Überprüfung der Kompatibilitätsergebnisse ist die Softwareupdate-Bereitstellungsphase der Vorgang der Bereitstellung von Softwareupdates. Planen Sie zum Installieren von Updates eine Bereitstellung, die Ihrem Releasezeitplan und Wartungsfenster entspricht. Sie können auswählen, welche Updatetypen in die Bereitstellung eingeschlossen werden sollen. Beispielsweise können Sie kritische oder Sicherheitsupdates einschließen und Updaterollups ausschließen.

Lesen Sie [Bereitstellen von Updates und Überprüfen von Ergebnissen](deploy-updates.md), um zu erfahren, wie Sie eine Updatebereitstellung planen.

## <a name="exclude-updates"></a>Ausschließen von Updates

Bei einigen Linux-Varianten (z. B. Red Hat Enterprise Linux) werden unter Umständen Upgrades auf Betriebssystemebene über Pakete durchgeführt. Dies kann ggf. zur Ausführung der Updateverwaltung und so zu einer Änderung der Versionsnummer des Betriebssystems führen. Dieses Verhalten ist beabsichtigt, da die Updateverwaltung die gleichen Methoden für Updatepakete verwendet, die auch ein Administrator lokal auf dem Linux-Computer nutzt.

Um zu vermeiden, dass die Betriebssystemversion durch Updateverwaltungsbereitstellungen aktualisiert wird, verwenden Sie das Feature **Ausschluss**.

In Red Hat Enterprise Linux lautet der Name des auszuschließenden Pakets `redhat-release-server.x86_64`.

## <a name="linux-update-classifications"></a>Linux-Updateklassifizierungen

Beim Bereitstellen von Updates für einen Linux-Computer können Sie Updateklassifizierungen auswählen. Durch diese Option werden die Updates gefiltert, die die definierten Kriterien erfüllen. Dieser Filter wird lokal auf dem Computer angewendet, wenn das Update bereitgestellt wird.

Da die Updateverwaltung die Updateanreicherung in der Cloud durchführt, können Sie einige Updates in der Updateverwaltung als Update mit Sicherheitsauswirkung kennzeichnen, auch wenn der lokale Computer nicht über diese Informationen verfügt. Es kann beim Anwenden kritischer Updates auf einem Linux-Computer möglicherweise Updates geben, die für diesen Computer nicht als Update mit Sicherheitsauswirkung gekennzeichnet sind und somit nicht angewandt werden. Es kann allerdings sein, dass der Computer von der Updateverwaltung weiterhin als nicht konform gemeldet wird, da sie über zusätzliche Informationen zum relevanten Update verfügt.

Das Bereitstellen von Updates nach Updateklassifizierung funktioniert unter RTM-Versionen von CentOS nicht. Wählen Sie zum ordnungsgemäßen Bereitstellen von Updates für CentOS alle Klassifizierungen aus, um sicherzustellen, dass die Updates angewendet werden. Wenn für SUSE als Klassifizierung NUR die Option **Weitere Updates** ausgewählt wird, kann dies dazu führen, dass durch die Klassifizierung andere Sicherheitsupdates installiert werden, wenn diese mit zypper (Paket-Manager) im Zusammenhang stehen oder dessen Abhängigkeiten zuerst erforderlich sind. Dieses Verhalten ist eine Einschränkung von zypper. In einigen Fällen müssen Sie die Updatebereitstellung u. U. erneut ausführen und dann die Bereitstellung über das Updateprotokoll überprüfen.

## <a name="review-update-deployments"></a>Überprüfen von Updatebereitstellungen

Nachdem die Bereitstellung abgeschlossen ist, überprüfen Sie den Prozess, um den Erfolg der Updatebereitstellung nach Computer oder Zielgruppe zu ermitteln. Weitere Informationen zum Überwachen des Bereitstellungsstatus finden Sie unter [Überprüfen des Bereitstellungsstatus](deploy-updates.md#check-deployment-status).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Erstellen von Warnungen, die Sie über die Ergebnisse der Updatebereitstellung informieren, finden Sie unter [Erstellen von Warnungen für die Updateverwaltung](configure-alerts.md).

* Sie können [Azure Monitor-Protokolle abfragen](query-logs.md), um Updatebewertungen, Bereitstellungen und andere verwandte Verwaltungsaufgaben zu analysieren. Es umfasst vordefinierte Abfragen, die Ihnen beim Einstieg helfen sollen.
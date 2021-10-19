---
title: Anzeigen von Azure Automation-Updatebewertungen
description: In diesem Artikel erfahren Sie, wie Sie Updatebewertungen für Bereitstellungen der Updateverwaltung anzeigen.
services: automation
ms.subservice: update-management
ms.date: 06/10/2021
ms.topic: conceptual
ms.openlocfilehash: 1607ad99ffb78a90bcc143ab20829709cec77561
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706493"
---
# <a name="view-update-assessments-in-update-management"></a>Anzeigen von Updatebewertungen in der Updateverwaltung

In der Updateverwaltung können Sie Informationen zu Ihren Computern, zu fehlenden Updates, zu Updatebereitstellungen und zu geplanten Updatebereitstellungen anzeigen. Sie können die Bewertungsinformationen für die ausgewählte Azure-VM, den ausgewählten Azure Arc-fähigen Server oder das Automation-Konto übergreifend für alle konfigurierten Computer und Server anzeigen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com)

## <a name="view-update-assessment"></a>Anzeigen der Updatebewertung

Navigieren Sie zum Anzeigen der Updatebewertung über einen virtuellen Azure-Computer zu **Virtual Machines**, und wählen Sie Ihren virtuellen Computer in der Liste aus. Wählen Sie im Menü auf der linken Seite die Option **Gast- und Hostupdates** aus, und greifen Sie auf der Seite **Gast- und Hostupdates** dann auf die **Updateverwaltung** zu.

In der Updateverwaltung können Sie Informationen zu Ihrem Computer, zu fehlenden Updates, zu Updatebereitstellungen und zu geplanten Updatebereitstellungen anzeigen.

[ ![Updateverwaltung: Ansicht für Azure-VM](./media/view-update-assessments/update-assessment-azure-vm.png)](./media/view-update-assessments/update-assessment-azure-vm-expanded.png#lightbox)

Navigieren Sie zum Anzeigen der Updatebewertung über einen Azure Arc-fähigen Server zu **Server – Azure Arc**, und wählen Sie Ihren Server in der Liste aus. Wählen Sie im Menü auf der linken Seite die Option **Gast- und Hostupdates** aus. Navigieren Sie auf der Seite **Gast- und Hostupdates** zur **Updateverwaltung**.

In der Updateverwaltung können Sie Informationen zu Ihrem Azure Arc-fähigen Computer, zu fehlenden Updates, zu Updatebereitstellungen und zu geplanten Updatebereitstellungen anzeigen.

[ ![Updateverwaltung: Bewertungsansicht für Arc-fähige Server](./media/view-update-assessments/update-assessment-arc-server.png)](./media/view-update-assessments/update-assessment-arc-server-expanded.png#lightbox)

Navigieren Sie zum übergreifenden Anzeigen der Updatebewertung für alle Computer, einschließlich der Azure Arc-fähigen Server Ihres Automation-Kontos, zu **Automation-Konten**, und wählen Sie Ihr Automation-Konto mit aktivierter Updateverwaltung in der Liste aus. Wählen Sie in Ihrem Automation-Konto im Menü auf der linken Seite die Option **Updateverwaltung** aus.

Die Updates für Ihre Umgebung sind auf der Seite **Updateverwaltung** aufgeführt. Falls Updates fehlen, wird auf der Registerkarte **Fehlende Updates** eine Liste mit den entsprechenden Updates angezeigt.

[ ![Standardansicht der Updateverwaltung](./media/overview/update-management-view.png)](./media/overview/update-management-view-expanded.png#lightbox)

Die Spalte **KONFORMITÄT** gibt Aufschluss darüber, wann der Computer zuletzt bewertet wurde. Die Spalte **UPDATE-AGENT-BEREITSCHAFT** gibt Aufschluss über die Integrität des Update-Agents. Liegt ein Problem vor, wählen Sie den Link aus, um zur Dokumentation für die Problembehandlung zu navigieren und Informationen zur Behebung des Problems zu erhalten.

Wählen Sie unter **Informationslink** den Link für ein Update aus, um den Supportartikel mit wichtigen Informationen zum Update zu öffnen.

[ ![Anzeigen des Updatestatus](./media/view-update-assessments/missing-updates.png)](./media/view-update-assessments/missing-updates-expanded.png#lightbox)

> [!NOTE]
> Informationen, die zum Status der Windows Defender-Definitionsupdates angezeigt werden, basieren auf den letzten Daten, die im Log Analytics-Arbeitsbereich zusammengefasst wurden und möglicherweise nicht aktuell sind. Weitere Informationen zu diesem Verhalten finden Sie unter [Szenario: Windows Defender-Update wird immer als fehlend angezeigt](../troubleshoot/update-management.md#windows-defender-update-missing-status).
 
Klicken Sie im Update auf eine andere Stelle, um den Bereich „Protokollsuche“ zu öffnen. Die Abfrage für die Protokollsuche ist für das jeweilige Update vordefiniert. Sie können diese Abfrage ändern oder eine eigene erstellen, um ausführliche Informationen anzuzeigen.

[ ![Anzeigen von Protokollabfrageergebnissen](./media/view-update-assessments/logsearch-results.png)](./media/view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>Anzeigen fehlender Updates

Wählen Sie **Fehlende Updates** aus, um eine Liste der Updates anzuzeigen, die auf Ihrem Computer nicht vorhanden sind. Jedes Update wird aufgeführt und kann ausgewählt werden. Es werden Informationen zur Anzahl von Computern, die das Update benötigen, sowie Details zum Betriebssystem und ein Link zu weiteren Informationen angezeigt. Im Bereich „Protokollsuche“ werden weitere Details zu den Updates angezeigt.

![Fehlende Updates](./media/view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>Verwenden von Updateklassifizierungen

Die folgenden Tabellen enthalten eine Liste der unterstützten Updateklassifizierungen in der Updateverwaltung sowie eine Definition für jede Klassifikation.

### <a name="windows"></a>Windows

|Klassifizierung  |BESCHREIBUNG  |
|---------|---------|
|Kritische Updates     | Updates für bestimmte Probleme, mit denen kritische, nicht sicherheitsrelevante Fehler behoben werden.        |
|Sicherheitsupdates     | Updates für produktspezifische, sicherheitsrelevante Probleme.        |
|Updaterollups     | Eine Gruppe von Hotfixes, die zur Vereinfachung der Bereitstellung gebündelt sind.        |
|Feature Packs     | Neue Produktfeatures, die nicht im Rahmen eines Produktreleases verteilt werden.        |
|Service Packs     | Eine Gruppe von Hotfixes, die auf eine Anwendung angewendet werden.        |
|Definitionsupdates     | Updates für Viren- oder andere Definitionsdateien.        |
|Tools     | Hilfsprogramme oder Features, mit denen mindestens eine Aufgabe ausgeführt werden kann.        |
|Aktualisierungen     | Updates für Anwendungen oder Dateien, die derzeit installiert sind.        |

### <a name="linux"></a>Linux

|Klassifizierung  |BESCHREIBUNG  |
|---------|---------|
|Kritische Updates und Sicherheitsupdates     | Updates für ein spezielles oder produktspezifisches, sicherheitsrelevantes Problem.         |
|Andere Updates     | Alle anderen Updates, die nicht kritisch sind oder bei denen es sich nicht um Sicherheitsupdates handelt.        |

Für Linux kann die Updateverwaltung bei der Anzeige von Bewertungsdaten zwischen kritischen Updates und Sicherheitsupdates in der Cloud unterscheiden. (Diese Granularität wird durch die Datenanreicherung in der Cloud ermöglicht.) Für das Patchen verwendet die Updateverwaltung Klassifizierungsdaten, die auf dem Computer verfügbar sind. Im Gegensatz zu anderen Distributionen verfügt CentOS in den RTM-Versionen des Produkts standardmäßig nicht über diese Informationen. Wenn Sie CentOS-Computer so konfiguriert haben, dass für den folgenden Befehl Sicherheitsdaten zurückgegeben werden, kann die Updateverwaltung basierend auf Klassifizierungen Patchvorgänge ausführen.

```bash
sudo yum -q --security check-update
```

Aktuell steht keine unterstützte Methode zur Verfügung, mit der unter CentOS eine native Verfügbarkeit von Klassifizierungsdaten implementiert werden kann. Für Kunden, die diese Funktion möglicherweise selbst implementiert haben, wird derzeit nur Support nach bestem Wissen bereitgestellt.

Sie müssen das Plug-In „yum-security“ installieren, um Updates in Version 6 von Red Hat Enterprise zu klassifizieren. Unter Red Hat Enterprise Linux 7 ist das Plug-In bereits Teil von yum selbst. Sie müssen also nichts installieren. Weitere Informationen finden Sie in [diesem Red Hat-Knowledge-Artikel](https://access.redhat.com/solutions/10021).

## <a name="next-steps"></a>Nächste Schritte

Die nächste Phase des Prozesses umfasst das [Bereitstellen von Updates](deploy-updates.md) auf nicht konformen Computern sowie das Überprüfen der Bereitstellungsergebnisse.

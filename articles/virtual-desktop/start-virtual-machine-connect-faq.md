---
title: 'Azure Virtual Desktop: FAQ zu „VM bei Verbindung starten“: Azure'
description: Häufig gestellte Fragen und bewährte Methoden zur Verwendung des Features „VM bei Verbindung starten“.
author: Heidilohr
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0ba2fcbc404a17b4b31d48b4e7d2e540a875f4f1
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444069"
---
# <a name="start-vm-on-connect-faq"></a>Häufig gestellte Fragen zum Starten von virtuellen Computern bei der Verbindungsherstellung

Dieser Artikel behandelt häufig gestellte Fragen zum Feature „VM bei Verbindung starten“ für Azure Virtual Desktop-Hostpools.

## <a name="are-vms-automatically-deallocated-when-a-user-stops-using-them"></a>Wird die Zuordnung der virtuellen Computer automatisch aufgehoben, wenn ein Benutzer deren Verwendung beendet?

Nein. Sie müssen zusätzliche Richtlinien konfigurieren, um Benutzer von ihren Sitzungen abzumelden, und Azure-Automatisierungsskripts ausführen, um die Zuordnung von VMs aufzuheben.

So konfigurieren Sie die Richtlinie zum Aufheben der Zuordnung:

1. Stellen Sie eine Remoteverbindung mit der VM her, für die Sie die Richtlinie festlegen möchten.

2. Öffnen Sie den lokalen **Gruppenrichtlinien-Editor**, und navigieren Sie zu **Richtlinie für lokalen Computer** > **Computerkonfiguration** > **Administrative Vorlagen** > **Windows-Komponenten** > **Remotedesktopdienste** > **Remotedesktop-Sitzungshost** > **Sitzungszeitlimits**.

3. Suchen Sie nach der Richtlinie **Zeitlimit für getrennte Sitzungen festlegen**, und ändern Sie deren Wert in **Aktiviert**.

4. Nachdem Sie die Richtlinie aktiviert haben, wählen Sie **Getrennte Sitzung beenden** aus.

>[!NOTE]
>Stellen Sie sicher, dass das Zeitlimit für die Richtlinie „Getrennte Sitzung beenden“ auf einen höheren Wert als fünf Minuten festgelegt ist. Ein niedriges Zeitlimit kann dazu führen, dass Sitzungen von Benutzern beendet werden, wenn Ihr Netzwerk die Verbindung zu lange verliert, was zu einem Arbeitsverlust führen kann.

Wenn Benutzer sich abmelden, wird die Zuordnung ihrer VMs nicht aufgehoben. Wie Sie die Zuordnung von VMs aufheben können, erfahren Sie unter [Starten oder Beenden von VMs außerhalb der Geschäftszeiten](../automation/automation-solution-vm-management.md) für persönliche Hostpools und [Skalieren von Sitzungshosts mithilfe von Azure Automation](set-up-scaling-script.md) für gepoolte Hostpools.

## <a name="can-users-turn-off-the-vm-from-their-clients"></a>Können Benutzer die VM von ihren Clients aus ausschalten?

Ja. Benutzer können den virtuellen Computer während der Sitzung wie einen physischen Computer im Startmenü herunterfahren. Wenn sie die VM herunterfahren, wird deren Zuordnung jedoch nicht aufgehoben. Wie Sie die Zuordnung von VMs aufheben können, erfahren Sie unter [Starten oder Beenden von VMs außerhalb der Geschäftszeiten](../automation/automation-solution-vm-management.md) für persönliche Hostpools und [Skalieren von Sitzungshosts mithilfe von Azure Automation](set-up-scaling-script.md) für gepoolte Hostpools.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Konfigurieren von „VM bei Verbindung starten“ finden Sie unter [VM bei Verbindung starten](start-virtual-machine-connect.md).

Antworten auf allgemeinere Fragen zu Azure Virtual Desktop finden Sie in diesem [FAQ](faq.yml)-Artikel.

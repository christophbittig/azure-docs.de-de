---
title: Verwalten eines VM-Pools in Azure Lab Services von Teams aus
description: Erfahren Sie, wie Sie einen VM-Pool in Azure Lab Services von Teams aus verwalten.
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 035344fa947403b77c35d716636472730d65752b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180151"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Verwalten eines VM-Pools in Lab Services von Teams aus

Die Erstellung virtueller Computer (VM) wird gestartet, sobald die Vorlagen-VM zum ersten Mal veröffentlicht wird. Eine der Anzahl der Benutzer in der Labbenutzerliste entsprechende Anzahl von VMs wird erstellt. VMs werden Lernenden automatisch bei ihrer ersten Anmeldung bei Azure Lab Services zugewiesen. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>Veröffentlichen einer Vorlage und Verwalten eines VM-Pools

Wechseln Sie zum Veröffentlichen der Vorlage zum Fenster „Teams Lab Services“, und wählen Sie Registerkarte **Vorlage** -> **...**  -> **Veröffentlichen** aus.

Sobald die Vorlagen-VM konfiguriert ist und der Dozent die Veröffentlichung der Vorlage auswählt, wird die Anzahl der virtuellen Computer erstellt, die der Anzahl der Benutzer in der Labbenutzerliste entspricht. Sobald das Lab veröffentlicht ist und virtuelle Computer erstellt wurden, werden die Benutzer automatisch beim Lab registriert, und die VMs werden ihnen bei ihrer ersten Anmeldung bei Azure Lab Services zugewiesen, d. h. wenn sie zum ersten Mal auf die Registerkarte **Azure Lab Services**-App zugreifen. 

Wenn eine Synchronisierung der Benutzerliste ausgelöst wird, wird die Labkapazität (Anzahl der virtuellen Computer im Lab) basierend auf den Änderungen der Teammitgliedschaft automatisch aktualisiert. Neue VMs werden erstellt, wenn neue Benutzer hinzugefügt werden, und VMs, die Benutzern zugewiesen sind, die aus dem Team entfernt wurden, werden entsprechend gelöscht. Weitere Informationen finden Sie unter [Verwalten von Lab Services-Benutzerlisten über Teams](how-to-manage-user-lists-within-teams.md). 

Dozenten können weiterhin direkt über die Registerkarte „VM-Pool“ auf VMs von Lernenden zugreifen. Auf VMs, die ihnen selbst zugewiesen sind, können Dozenten entweder über die Registerkarte **VM-Pool** oder durch Klicken auf die Schaltfläche **Meine virtuellen Computer** (obere/rechte Ecke des Bildschirms) zugreifen. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="VM-Pool":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in folgenden Artikeln:

- [Verwenden von Azure Lab Services in Teams: Übersicht](lab-services-within-teams-overview.md)
- [Erste Schritte und Erstellen eines Lab Services-Labs aus Teams](how-to-get-started-create-lab-within-teams.md)
- [Verwalten von Lab Services-Benutzerlisten aus Teams](how-to-manage-user-lists-within-teams.md)
- [Erstellen und Verwalten von Lab Services-Zeitplänen in Teams](how-to-create-schedules-within-teams.md)
- [Zugreifen auf eine VM (Ansicht für Lernende) in Lab Services in Teams](how-to-access-vm-for-students-within-teams.md)



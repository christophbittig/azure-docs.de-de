---
title: Erstellen von Azure Lab Services in Teams
description: Erfahren Sie, wie Sie Lab Services-Zeitpläne in Teams erstellen.
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 5427105d0bac3fd652764345c1d31fd0ef8bb2db
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180569"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Erstellen und Verwalten von Lab Services-Zeitplänen in Teams

Mithilfe von Zeitplänen können Sie ein Classroom-Lab so konfigurieren, dass VMs im Lab automatisch zu einem bestimmten Zeitpunkt gestartet und heruntergefahren werden. Sie können einen einmaligen Zeitplan oder einen sich wiederholenden Zeitplan definieren. Die folgenden Verfahren zeigen die Schritte zum Erstellen und Verwalten von Zeitplänen für ein Classroom-Lab: 

Zeitpläne beeinflussen virtuelle Labcomputer folgendermaßen: 

- Die Vorlagen-VM ist nicht in den Zeitplänen enthalten. 
- Es werden nur zugewiesene virtuelle Computer gestartet. Das bedeutet, dass der Computer nur zu den geplanten Stunden gestartet wird, wenn er nicht von einem Endbenutzer (Kursteilnehmer) beansprucht wird. 
- Alle virtuellen Computer (unabhängig davon, ob sie von einem Benutzer beansprucht werden) werden basierend auf dem Labzeitplan angehalten. 

> [!IMPORTANT]
> Die geplante Ausführungszeit von virtuellen Computern wird nicht mit dem Kontingent eines Benutzers verrechnet. Das Kontingent dient für die Zeiten außerhalb der Stunden im Zeitplan, die ein Kursteilnehmer mit VMs verbringt. 

Benutzer können Lab-Zeitpläne in Teams wie auf der [Labs-Website](https://labs.azure.com) erstellen, bearbeiten und löschen. Weitere Informationen finden Sie im Artikel zum [Erstellen und Verwalten von Zeitplänen](how-to-create-schedules-within-teams.md).

## <a name="automatic-shutdown-and-disconnect-settings"></a>Einstellungen für automatisches Herunterfahren und Trennen

Sie können mehrere Features zur Kostenkontrolle beim automatischen Herunterfahren aktivieren, um proaktiv zusätzliche Kosten zu verhindern, wenn die virtuellen Computer nicht aktiv genutzt werden. Die Kombination der folgenden drei Features zum automatischen Herunterfahren und Trennen deckt den größten Teil der Fälle ab, in denen Benutzer die Ausführung der virtuellen Computer versehentlich nicht beenden:
 
- Automatisches Trennen von Benutzern von virtuellen Computern, die vom Betriebssystem als im Leerlauf erkannt werden
- Automatisches Herunterfahren virtueller Computer, wenn Benutzer die Verbindung trennen
- Automatisches Herunterfahren von virtuellen Computern, die gestartet wurden, mit denen jedoch von den Benutzern keine Verbindung hergestellt wurde.

Weitere Informationen finden Sie im Artikel [Konfigurieren von Einstellungen für automatisches Herunterfahren für ein Lab](how-to-enable-shutdown-disconnect.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in folgenden Artikeln:

- [Verwenden von Azure Lab Services in Teams: Übersicht](lab-services-within-teams-overview.md)
- [Erste Schritte und Erstellen eines Labs in Teams](how-to-get-started-create-lab-within-teams.md)
- [Verwalten von Lab-Benutzerlisten in Teams](how-to-manage-user-lists-within-teams.md)
- [Verwalten des VM-Pools des Labs in Teams](how-to-manage-vm-pool-within-teams.md)
- [Zugreifen auf eine VM innerhalb von Teams: Kursteilnehmeransicht](how-to-access-vm-for-students-within-teams.md)

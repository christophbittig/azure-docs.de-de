---
title: Verwalten von Azure Lab Services-Benutzerlisten aus Teams
description: Erfahren Sie, wie Sie Azure Lab Services-Benutzerlisten aus Teams verwalten.
ms.topic: how-to
ms.date: 10/07/2020
ms.openlocfilehash: 598edd21943364d25f59848236f838cb10fa3ce5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180189"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>Verwalten von Lab Services-Benutzerlisten aus Teams

Wenn ein Lab innerhalb von Teams erstellt wird (siehe [Erste Schritte und Erstellen eines Lab Services-Labs aus Teams](how-to-get-started-create-lab-within-teams.md)), wird die Lab-Benutzerliste automatisch mit Daten aufgefüllt und mit der Teammitgliedschaft synchronisiert. Jeder im Team, einschließlich Besitzern, Mitgliedern und Gästen, wird automatisch der Lab-Benutzerliste hinzugefügt. Azure Lab Services führt eine Synchronisierung mit der Teammitgliedschaft durch. Eine automatische Synchronisierung wird alle 24 Stunden ausgelöst. 

## <a name="sync-users"></a>Synchronisieren von Benutzern

Dozenten können über die Schaltfläche **Sync** eine manuelle Synchronisierung initiieren, sobald die Teammitgliedschaft aktualisiert wurde. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="Synchronisieren von Benutzern":::

Nach Abschluss der automatischen oder manuellen Synchronisierung gilt Folgendes abhängig davon, ob das Lab veröffentlicht wurde.

* Wenn das Lab nicht mindestens einmal veröffentlicht wurde:
    * Benutzer werden in der Labbenutzerliste gemäß den Änderungen der Teammitgliedschaft hinzugefügt oder gelöscht. 
* Wenn das Lab mindestens einmal veröffentlicht wurde, wird zusätzlich zum Hinzufügen oder Löschen von Benutzern automatisch die Labkapazität aktualisiert.
    * Bei neuen Ergänzungen des Teams werden neue VMs erstellt.
    * Wenn ein Benutzer aus dem Team gelöscht wurde, wird auch die zugehörige VM gelöscht.

## <a name="next-steps"></a>Nächste Schritte

Sobald die Vorlagen-VM konfiguriert ist und der Dozent die Veröffentlichung der Vorlage auswählt, wird die Anzahl der virtuellen Computer erstellt, die der Anzahl der Benutzer in der Labbenutzerliste entspricht. Sobald das Lab veröffentlicht ist und virtuelle Computer erstellt wurden, werden die Benutzer automatisch beim Lab registriert, und die VMs werden ihnen bei ihrer ersten Anmeldung bei Azure Lab Services zugewiesen, d. h. wenn sie zum ersten Mal auf die Registerkarte **Azure Lab Services**-App zugreifen. 

Wechseln Sie zum Veröffentlichen der Vorlagen-VM zum Fenster „Teams Lab Services“, und wählen Sie Registerkarte **Vorlage** -> **...**  -> **Veröffentlichen** aus.

Informationen zum Verwalten von VM-Pools finden Sie unter [Verwalten eines VM-Pools in Lab Services von Teams aus](how-to-manage-vm-pool-within-teams.md).

### <a name="also-review"></a>Lesen Sie außerdem:

Weitere Informationen finden Sie in folgenden Artikeln:

- [Verwenden von Azure Lab Services in Teams: Übersicht](lab-services-within-teams-overview.md)
- [Erste Schritte und Erstellen eines Lab Services-Labs aus Teams](how-to-get-started-create-lab-within-teams.md)
- [Erstellen und Verwalten von Lab Services-Zeitplänen in Teams](how-to-create-schedules-within-teams.md)
- [Zugreifen auf eine VM (Ansicht für Lernende) in Lab Services in Teams](how-to-access-vm-for-students-within-teams.md)


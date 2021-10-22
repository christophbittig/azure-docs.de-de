---
title: Zurücksetzen von Kennwörtern für virtuelle Computer in Classroom-Labs in Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Kennwörter für virtuelle Computer (VMs) in Labs in Azure Lab Services zurücksetzen.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: ca311c08419ff54fdfe72b20a2a2bffbc436d1dd
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180132"
---
# <a name="set-or-reset-password-for-virtual-machines-in-labs-students"></a>Festlegen oder Zurücksetzen von Kennwörtern für virtuelle Computer in Labs (Kursteilnehmer)
In diesem Artikel erfahren Sie, wie Kursteilnehmer das Kennwort für ihre virtuellen Computer festlegen oder zurücksetzen können. 

## <a name="enable-resetting-of-passwords"></a>Aktivieren der Zurücksetzung von Kennwörtern
Der Lab-Besitzer kann zum Zeitpunkt der Erstellung des Labs die Option **Use same password for all virtual machines** (Dasselbe Kennwort für alle virtuellen Computer verwenden) aktivieren oder deaktivieren. Wenn diese Option aktiviert wurde, können die Kursteilnehmer das Kennwort nicht zurücksetzen. Alle virtuellen Computer im Lab haben dasselbe Kennwort, das vom Kursleiter festgelegt wurde. 

Wenn diese Option deaktiviert ist, müssen die Benutzer ein Kennwort festgelegt, wenn sie zum ersten Mal eine Verbindung mit dem virtuellen Computer herstellen. Kursteilnehmer können das Kennwort auch später jederzeit zurücksetzen, wie es im letzten Abschnitt dieses Artikels angegeben ist. 

## <a name="reset-password-for-the-first-time"></a>Erstmaliges Zurücksetzen des Kennworts
Wenn die Option **Use same password for all virtual machines** (Dasselbe Kennwort für alle virtuellen Computer verwenden) deaktiviert wurde, wird Benutzern (Kursteilnehmern), die die Schaltfläche **Verbinden** auf der Lab-Kachel auf der Seite **Meine virtuellen Computer** auswählen, das folgende Dialogfeld zum Festlegen des Kennworts für den virtuellen Computer angezeigt: 

![Zurücksetzen des Kennworts für Kursteilnehmer](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Späteres Zurücksetzen des Kennworts
Die Kursteilnehmer können das Kennwort auch festlegen, indem sie auf das Überlaufmenü (**drei vertikale Punkte**) auf der Lab-Kachel klicken, und **Kennwort zurücksetzen** auswählen. 

![Späteres Zurücksetzen des Kennworts](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Nächste Schritte
Informationen zu anderen Verwendungsoptionen für Kursteilnehmer, die ein Lab-Besitzer konfigurieren kann, finden Sie im folgenden Artikel: [Konfigurieren von Nutzungseinstellungen und Richtlinien](how-to-configure-student-usage.md).

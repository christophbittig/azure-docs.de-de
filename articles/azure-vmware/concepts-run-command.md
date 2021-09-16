---
title: 'Konzepte: Skriptausführungen in Azure VMware Solution'
description: Hier erfahren Sie mehr über die Verwendung von Skriptausführungen in Azure VMware Solution.
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 28cbf76dd035ce9b04909a61e3001063aa151162
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310676"
---
# <a name="run-commands-in-azure-vmware-solution"></a>Skriptausführungen in Azure VMware Solution

In Azure VMware Solution erhalten Sie mit der Rolle „CloudAdmin“ vCenter-Zugriff. Sie können die [Berechtigungen anzeigen](concepts-identity.md#view-the-vcenter-privileges), die der Azure VMware Solution-Rolle „CloudAdmin“ für Ihre private Azure VMware Solution-Cloud in vCenter erteilt wurden. Skriptausführungen stellen eine Sammlung von PowerShell-Cmdlets dar, die Sie für bestimmte Vorgänge in vCenter ausführen, für die erhöhte Rechte erforderlich sind. 

Azure VMware Solution unterstützt die folgenden Vorgänge:

- [Installieren und Deinstallieren der JetStream-Notfallwiederherstellungslösung](deploy-disaster-recovery-using-jetstream.md)

- [Konfigurieren einer externen Identitätsquelle](configure-identity-source-vcenter.md)

- [Anzeigen und Bearbeiten der Speicherrichtlinie](configure-storage-policy.md) 


>[!NOTE]
>Skriptausführungen werden nacheinander in der Reihenfolge ausgeführt, in der sie übermittelt werden.

## <a name="view-the-status-of-an-execution"></a>Anzeigen des Status einer Ausführung

Sie können den Status einer ausgeführten Skriptausführung anzeigen, einschließlich Ausgabe, Fehler, Warnungen und Informationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie auf **Run command** > **Run execution status** („Skriptausführung“ > „Status der Ausführung“).

   Sie können nach Ausführungsname, Paketname, Paketversion, Befehlsname, Startzeit, Beendigungszeit und Status sortieren.  

   :::image type="content" source="media/run-command/run-execution-status.png" alt-text="Screenshot: Registerkarte „Run execution status“ (Status der Ausführung)" lightbox="media/run-command/run-execution-status.png":::

1. Wählen Sie die Ausführung aus, die Sie anzeigen möchten.

   :::image type="content" source="media/run-command/run-execution-status-example.png" alt-text="Screenshot: Beispiel einer Ausführung":::

   Sie können weitere Details zur Ausführung anzeigen, einschließlich der Ausgabe, Fehler, Warnungen und Informationen.

   - **Details:** Zusammenfassung der Ausführungsdetails, z. B. Name, Status, Package und Name der Skriptausführung. 

   - **Ausgabe:** Meldung am Ende der erfolgreichen Ausführung eines Cmdlets. Nicht alle Cmdlets verfügen über eine Ausgabe.

      :::image type="content" source="media/run-command/run-execution-status-example-output.png" alt-text="Screenshot: Ausgabe einer Ausführung":::

   - **Fehler:** Ausnahme für Abbruch, die die Ausführung eines Cmdlets beendet hat.    

      :::image type="content" source="media/run-command/run-execution-status-example-error.png" alt-text="Screenshot: Fehler, die während der Ausführung ermittelt wurden":::

   - **Warnung:** Während der Ausführung eines Cmdlets ist eine Ausnahme ohne Abbruch aufgetreten. 

      :::image type="content" source="media/run-command/run-execution-status-example-warning.png" alt-text="Screenshot: Warnungen, die während der Ausführung ermittelt wurden":::

   - **Informationen:** Statusmeldung während der Ausführung eines Cmdlets. 

      :::image type="content" source="media/run-command/run-execution-status-example-information.png" alt-text="Screenshot: Gesamtfortschritt der Ausführung des Cmdlets in Echtzeit":::



## <a name="cancel-or-delete-a-job"></a>Abbrechen oder Löschen eines Auftrags



### <a name="method-1"></a>Methode 1

>[!NOTE]
>Methode 1 kann nicht rückgängig gemacht werden.

1. Klicken Sie auf **Run command** > **Run execution status** („Skriptausführung“ > „Status der Ausführung“), und wählen Sie dann den Auftrag aus, den Sie abbrechen möchten.

   :::image type="content" source="media/run-command/run-execution-cancel-delete-job-method-1.png" alt-text="Screenshot: Abbrechen und Löschen einer Skriptausführung":::

2. Wählen Sie **Ja** aus, um den Auftrag für alle Benutzer abzubrechen und zu entfernen.



### <a name="method-2"></a>Methode 2

1. Klicken Sie auf **Run command** > **Packages** > **Run execution status** („Skriptausführung“ > „Pakete“ > „Status der Ausführung“).

2. Klicken Sie für den Auftrag, den Sie abbrechen und löschen möchten, auf **Mehr** (...).

   :::image type="content" source="media/run-command/run-execution-cancel-delete-job-method-2.png" alt-text="Screenshot: Abbrechen und Löschen einer Skriptausführung über die Auslassungspunkte":::

3. Wählen Sie **Ja** aus, um den Auftrag für alle Benutzer abzubrechen und zu entfernen.



## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Konzepten der Skriptausführung vertraut gemacht haben, können Sie das Feature „Skriptausführung“ für Folgendes verwenden:

- [Konfigurieren der Speicherrichtlinie:](configure-storage-policy.md) Jedem virtuellen Computer, der in einem vSAN-Datenspeicher bereitgestellt wird, wird mindestens eine VM-Speicherrichtlinie zugewiesen. Sie können eine VM-Speicherrichtlinie bei der ersten Bereitstellung eines virtuellen Computers zuweisen oder wenn Sie andere VM-Vorgänge ausführen (z. B. Klonen oder Migrieren).

- [Konfigurieren externer Identitätsquellen für vCenter:](configure-identity-source-vcenter.md) vCenter verfügt über einen lokalen Benutzer namens „cloudadmin“, dem die Rolle „CloudAdmin“ zugewiesen ist. Mithilfe des lokalen cloudadmin-Benutzers werden Benutzer in Active Directory eingerichtet. Mit dem Feature „Skriptausführung“ können Sie Active Directory über LDAP oder LDAPS für vCenter als externe Identitätsquelle konfigurieren.

- [Bereitstellen der Notfallwiederherstellung mithilfe von JetStream:](deploy-disaster-recovery-using-jetstream.md) Speichern Sie Daten direkt in einem Wiederherstellungscluster in vSAN. Die Daten werden über E/A-Filter erfasst, die in vSphere ausgeführt werden. Der zugrunde liegende Datenspeicher kann VMFS, VSAN, vVol oder eine beliebige HCI-Plattform sein. 

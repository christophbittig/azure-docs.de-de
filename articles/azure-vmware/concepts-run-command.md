---
title: Konzepte – Ausführungsbefehl in Azure VMware Solution (Vorschau)
description: Hier erfahren Sie mehr über die Verwendung von Skriptausführungen in Azure VMware Solution.
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: a4121f8479b22eb1c0666a1e7d7a23ece4fb3d20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668847"
---
# <a name="run-command-in-azure-vmware-solution-preview"></a>Ausführungsbefehl in Azure VMware Solution (Vorschau)

In Azure VMware Solution verfügt vCenter über einen integrierten lokalen Benutzer namens *cloudadmin*, der der Rolle „CloudAdmin“ zugewiesen ist. Die CloudAdmin-Rolle verfügt über vCenter [Rechte](concepts-identity.md#view-the-vcenter-privileges), die sich von anderen VMware-Cloud-Lösungen und lokalen Bereitstellungen unterscheiden. Mit der Funktion Befehl ausführen (Vorschau) können Sie mithilfe einer Sammlung von PowerShell-Cmdlets Vorgänge ausführen, für die normalerweise erhöhte Rechte erforderlich wären. 

Azure VMware Solution unterstützt die folgenden Vorgänge:

- [Konfigurieren einer externen Identitätsquelle](configure-identity-source-vcenter.md)

- [Anzeigen und Festlegen von Speicherrichtlinien](configure-storage-policy.md) 

- [Bereitstellen der Notfallwiederherstellung mithilfe von JetStream](deploy-disaster-recovery-using-jetstream.md)


>[!NOTE]
>Skriptausführungen werden nacheinander in der Reihenfolge ausgeführt, in der sie übermittelt werden.

## <a name="view-the-status-of-an-execution"></a>Anzeigen des Status einer Ausführung

Sie können den Status von allen ausgeführten Ausführungsbefehlen anzeigen, einschließlich der Ausgabe, Fehler, Warnungen und Informationsprotokolle der Cmdlets.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie auf **Run command** > **Run execution status** („Skriptausführung“ > „Status der Ausführung“).

   Sie können nach den verschiedenen Spalten sortieren, indem Sie die Spalte auswählen.  

   :::image type="content" source="media/run-command/run-execution-status.png" alt-text="Screenshot: Registerkarte „Run execution status“ (Status der Ausführung)" lightbox="media/run-command/run-execution-status.png":::

1. Wählen Sie die Ausführung aus, die Sie anzeigen möchten. Es wird ein Bereich mit Details zur Ausführung sowie weitere Registerkarten für die verschiedenen Arten der vom Cmdlet generierten Ausgabe geöffnet.

   :::image type="content" source="media/run-command/run-execution-status-example.png" alt-text="Screenshot: Beispiel einer Ausführung":::

   Sie können weitere Details zur Ausführung anzeigen, einschließlich der Ausgabe, Fehler, Warnungen und Informationen.

   - **Details** – Zusammenfassung der Ausführungsdetails, z.B. Name, Status, Paket, Cmdlet-Name und Fehler, wenn der Befehl fehlgeschlagen ist. 

   - **Ausgabe** – Vom Cmdlet ausgegebene Meldungen. Kann den Fortschritt oder das Ergebnis des Vorgangs umfassen. Nicht alle Cmdlets verfügen über eine Ausgabe.

      :::image type="content" source="media/run-command/run-execution-status-example-output.png" alt-text="Screenshot: Ausgabe einer Ausführung":::

   - **Fehler** – Fehlermeldungen, die bei der Ausführung des Cmdlets generiert werden. Diese Meldung wird zusätzlich zur abschließenden Fehlermeldung im Detailbereich angezeigt.    

      :::image type="content" source="media/run-command/run-execution-status-example-error.png" alt-text="Screenshot: Fehler, die während der Ausführung ermittelt wurden":::

   - **Warnung** – Während der Ausführung generierte Warnmeldungen. 

      :::image type="content" source="media/run-command/run-execution-status-example-warning.png" alt-text="Screenshot: Warnungen, die während der Ausführung ermittelt wurden":::

   - **Information** – Während der Ausführung eines Cmdlets generierte Fortschritts- und Diagnosemeldungen. 

      :::image type="content" source="media/run-command/run-execution-status-example-information.png" alt-text="Screenshot: Gesamtfortschritt der Ausführung des Cmdlets in Echtzeit":::



## <a name="cancel-or-delete-a-job"></a>Abbrechen oder Löschen eines Auftrags



### <a name="method-1"></a>Methode 1

Mit dieser Methode wird versucht, die Ausführung abzubrechen, und der Auftrag wird nach Abschluss gelöscht.

>[!IMPORTANT]
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

- [Konfigurieren von Speicherrichtlinie](configure-storage-policy.md) – Jeder VM, die auf einem vSAN-Datenspeicher bereitgestellt wird, wird eine vSAN-Speicherrichtlinie zugewiesen. Sie können eine vSAN-Speicherrichtlinie bei der erstmaligen Bereitstellung einer VM oder bei anderen VM-Vorgängen zuweisen, z. B. beim Klonen oder Migrieren.

- [Konfigurieren von einer eine externen Identitätsquelle für vCenter (Befehl ausführen)](configure-identity-source-vcenter.md) – Konfigurieren von Active Directory über LDAP oder LDAPS für vCenter, was die Verwendung einer externen Identitätsquelle als Active Directory Domain Services aktiviert. Anschließend können Sie der CloudAdmin-Rolle Gruppen aus der externen Identitätsquelle hinzufügen.

- [Bereitstellen der Notfallwiederherstellung mithilfe von JetStream:](deploy-disaster-recovery-using-jetstream.md) Speichern Sie Daten direkt in einem Wiederherstellungscluster in vSAN. Die Daten werden über E/A-Filter erfasst, die in vSphere ausgeführt werden. Der zugrunde liegende Datenspeicher kann VMFS, VSAN, vVol oder eine beliebige HCI-Plattform sein. 
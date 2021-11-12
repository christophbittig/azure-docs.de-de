---
title: Wiederherstellen einer Instanz von Azure Database for MySQL Flexible Server über das Azure-Portal
description: In diesem Artikel wird beschrieben, wie Sie Wiederherstellungsvorgänge für Azure Database for MySQL Flexible Server über das Azure-Portal durchführen.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: cce31cc3d83be03da462c9345c1b8d3d86ef6228
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467910"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Zeitpunktwiederherstellung einer Azure-Datenbank für MySQL–flexible Server über das Azure-Portal

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

In diesem Artikel wird Schritt für Schritt beschrieben, wie Sie Zeitpunktwiederherstellungen in einer Flexible Server-Instanz mithilfe von Sicherungen durchführen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

- Sie müssen über Azure Database for MySQL Flexible Server verfügen.

## <a name="restore-to-the-latest-restore-point"></a>Wiederherstellen des letzten Wiederherstellungspunkts

Führen Sie die folgenden Schritte aus, um Ihre Flexible Server-Instanz mit der frühesten vorhandenen Sicherung wiederherzustellen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihre Flexible Server-Instanz aus, von der aus Sie die Sicherung wiederherstellen möchten.

2. Klicken Sie im linken Bereich auf **Übersicht**.

3. Klicken Sie auf der Seite „Übersicht“ auf **Wiederherstellen**.

4. Die Wiederherstellungsseite wird angezeigt, und Sie können zwischen **Neuester Wiederherstellungspunkt** und „Benutzerdefinierter Wiederherstellungspunkt“ auswählen.

5. Wählen Sie **Neuester Wiederherstellungspunkt** aus.

6. Geben Sie einen neuen Servernamen im Feld **Auf neuem Server wiederherstellen** ein.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-latest.png" alt-text="Frühester Wiederherstellungszeitpunkt":::

7. Klicken Sie auf **OK**.

8. Es wird eine Benachrichtigung angezeigt, dass der Wiederherstellungsvorgang eingeleitet wurde.


## <a name="restore-to-a-fastest-restore-point"></a>Wiederherstellen zu einem schnellsten Wiederherstellungspunkt

Führen Sie diese Schritte aus, um Ihren flexiblen Server mithilfe einer vorhandenen vollständigen Sicherung als schnellsten Wiederherstellungspunkt wiederherzustellen. 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihre Flexible Server-Instanz aus, von der aus Sie die Sicherung wiederherstellen möchten. 

2. Klicken Sie im linken Bereich auf **Übersicht**. 

3. Klicken Sie auf der Seite „Übersicht“ auf **Wiederherstellen**. 

4. Die Wiederherstellungsseite wird angezeigt und Sie können zwischen „Neuester Wiederherstellungspunkt“, „Benutzerdefinierter Wiederherstellungspunkt“ und „Schnellster Wiederherstellungspunkt“ auswählen. 

5. Wählen Sie die Option **Schnellsten Wiederherstellungspunkt (Wiederherstellung über vollständige Sicherung) auswählen**. 

6. Wählen Sie in der Dropdownliste **Schnellster Wiederherstellungspunkt (UTC)** die gewünschte vollständige Sicherung aus. 
 
    :::image type="content" source="./media/how-to-restore-server-portal/fastest-restore-point.png" alt-text="Schnellster Wiederherstellungspunkt":::

7. Geben Sie einen neuen Servernamen im Feld **Auf neuem Server wiederherstellen** ein.

8. Klicken Sie auf **Überprüfen + erstellen**. 

9. Nachdem Sie auf **Erstellen** geklickt haben, wird eine Benachrichtigung angezeigt, dass der Wiederherstellungsvorgang eingeleitet wurde.  

## <a name="restore-from-a-full-backup-through-the-backup-and-restore-blade"></a>Wiederherstellen aus einer vollständigen Sicherung über das Blatt "Sichern und Wiederherstellen"

Gehen Sie wie folgt vor, um Ihren flexiblen Server anhand einer vorhandenen vollständigen Sicherung wiederherzustellen. 

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihre Flexible Server-Instanz aus, von der aus Sie die Sicherung wiederherstellen möchten. 

2. Klicken Sie im linken Bereich auf **Sichern und Wiederherstellen**. 

3. Die Seite „Verfügbare Sicherungen anzeigen“ wird mit der Option zum Wiederherstellen aus allen vollständigen automatisierten Sicherungen angezeigt, die für den Server innerhalb des Aufbewahrungszeitraums erstellt wurden.  

4. Wählen Sie die gewünschte vollständige Sicherung aus der Liste aus, indem Sie auf die entsprechende **Wiederherstellungs**-Aktion klicken. 
 
    :::image type="content" source="./media/how-to-restore-server-portal/view-available-backups.png" alt-text="Liste der verfügbaren Sicherungen":::

5. Die Wiederherstellungsseite wird mit der standardmäßig ausgewählten Option „Schnellster Wiederherstellungspunkt“ und dem gewünschten vollständigen Sicherungszeitstempel, der auf der Seite „Liste der verfügbaren Sicherungen“ ausgewählt wurde, angezeigt. 

6. Geben Sie einen neuen Servernamen im Feld **Auf neuem Server wiederherstellen** ein.

7. Klicken Sie auf **Überprüfen + erstellen**. 

8. Nachdem Sie auf **Erstellen** geklickt haben, wird eine Benachrichtigung angezeigt, dass der Wiederherstellungsvorgang eingeleitet wurde.  


## <a name="geo-restore-to-latest-restore-point"></a>Geo-Wiederherstellung des letzten Wiederherstellungspunkts

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihre Flexible Server-Instanz aus, von der aus Sie die Sicherung wiederherstellen möchten.

2. Klicken Sie im linken Bereich auf **Übersicht**.

3. Klicken Sie auf der Seite „Übersicht“ auf **Wiederherstellen**.

4. Die Seite "Wiederherstellen" wird mit der Option „**Georedundante Wiederherstellung** auswählen“ angezeigt. Wenn Sie Ihren Server für geografisch redundante Sicherungen konfiguriert haben, kann der Server in der entsprechenden gekoppelten Azure-Region wiederhergestellt werden und die Option für die georedundante Wiederherstellung kann aktiviert werden. Mit der Option „Georedundante Wiederherstellung“ wird der Server zum neuesten UTC-Jetzt-Zeitstempel wiederhergestellt. Daher können nach der Auswahl der georedundanten Wiederherstellung die Optionen für die Zeitpunktwiederherstellung nicht gleichzeitig ausgewählt werden.

   :::image type="content" source="./media/how-to-restore-server-portal/georestore-flex.png" alt-text="Geo-Wiederherstellungsoptionen":::

   :::image type="content" source="./media/how-to-restore-server-portal/georestore-enabled-flex.png" alt-text="Aktivieren der Geo-Wiederherstellung":::

5. Geben Sie im Abschnitt „Serverdetails“ im Feld **Name** einen neuen Servernamen an.

6. Wählen Sie **Überprüfen + erstellen** aus, um ihre Auswahl zu überprüfen. 

7. Es wird eine Benachrichtigung angezeigt, dass der Wiederherstellungsvorgang eingeleitet wurde. Dieser Vorgang kann einige Minuten dauern. 

Der neue Server, der durch die Geo-Wiederherstellung erstellt wurde, verfügt über den gleichen Serveradministrator-Anmeldenamen und das dazugehörige Kennwort, die für den bereits bestehenden Server zum Zeitpunkt der Initiierung der Wiederherstellung gültig waren. Sie können das Kennwort auf der Seite Übersicht des neuen Servers ändern. Darüber hinaus können während einer Geo-Wiederherstellung die **Netzwerkeinstellungen** wie Einstellungen für virtuelle Netzwerke und Firewall-Regulierungen wie im folgenden Abschnitt beschrieben konfiguriert werden.

## <a name="using-restore-to-move-a-server-from-public-access-to-private-access"></a>Ändern des Zugriffs auf einen Server von „Öffentlich“ in „Privat“ mithilfe der Wiederherstellung

Führen Sie die folgenden Schritte aus, um Ihre Flexible Server-Instanz mit der frühesten vorhandenen Sicherung wiederherzustellen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihre Flexible Server-Instanz aus, von der aus Sie die Sicherung wiederherstellen möchten.

2. Klicken Sie auf der Seite „Übersicht“ auf **Wiederherstellen**.

3. Die Seite "Wiederherstellen" wird mit einer Option angezeigt, mit der Sie zwischen Geo-Wiederherstellungs- oder Zeitpunkt-Wiederherstellungsoptionen wählen können.

4. Wählen Sie entweder die Option **Geo-Wiederherstellung** oder **Zeitpunkt-Wiederherstellung** aus.

5. Geben Sie einen neuen Servernamen im Feld **Auf neuem Server wiederherstellen** ein.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-private-dns-zone.png" alt-text="Übersicht anzeigen":::

6. Wechseln Sie zur Registerkarte **Netzwerk**, um Netzwerkeinstellungen festzulegen.

7. Wählen Sie unter **Konnektivitätsmethode** die Option **Privater Zugriff (VNET-Integration)** aus. Wechseln Sie zum Abschnitt **Virtuelles Netzwerk**. Sie können entweder ein bereits vorhandenes *virtuelles Netzwerk* und *Subnetz* auswählen, das an *Microsoft.DBforMySQL/flexibleServers* delegiert ist, oder ein neues erstellen, indem Sie auf den Link *Virtuelles Netzwerk erstellen* klicken.
    > [!Note]
    > In der Dropdownliste werden nur virtuelle Netze und Subnetze in derselben Region und im selben Abonnement angezeigt. </br>
    > Das ausgewählte Subnetz wird an *Microsoft.DBforMySQL/flexibleServers* delegiert. Dies bedeutet, dass dieses Subnetz nur von flexiblen Azure Database for MySQL-Servern genutzt werden kann.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="VNet-Konfiguration":::

8. Erstellen Sie eine neue oder wählen Sie eine vorhandene **private DNS-Zone** aus.
    > [!NOTE]
    > Namen privater DNS-Zonen müssen mit `mysql.database.azure.com` enden. </br>
    > Wenn die Option zum Erstellen einer neuen privaten DNS-Zone nicht angezeigt wird, geben Sie den Servernamen auf der Registerkarte **Grundlagen** ein.</br>
    > Nachdem der flexible Server in einem virtuellen Netzwerk und Subnetz bereitgestellt wurde, können Sie ihn nicht mehr in „Öffentlicher Zugriff (zugelassene IP-Adressen)“ verschieben.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="DNS-Konfiguration":::
9. Wählen Sie **Überprüfen + erstellen** aus, um Ihre Flexible Server-Konfiguration zu überprüfen.
10. Wählen Sie **Erstellen** aus, um den Server bereitzustellen. Die Bereitstellung kann einige Minuten dauern.

11. Es wird eine Benachrichtigung angezeigt, dass der Wiederherstellungsvorgang eingeleitet wurde.


## <a name="perform-post-restore-tasks"></a>Durchführen der Aufgaben nach der Wiederherstellung

Nachdem die Wiederherstellung abgeschlossen ist, sollten Sie die folgenden Aufgaben durchführen, um Ihre Benutzer und Anwendungen wieder in den betriebsbereiten Zustand zu versetzen:

- Umleiten von Clients und Clientanwendungen an den neuen Server, wenn der neue Server den ursprünglichen Server ersetzen soll.
- Stellen Sie sicher, dass geeignete VNET-Regeln vorhanden sind, damit Benutzer eine Verbindung herstellen können. Diese Regeln werden nicht vom ursprünglichen Server kopiert.
- Sicherstellen, dass geeignete Anmeldungen und Berechtigungen auf Datenbankebene vorhanden sind.
- Konfigurieren von Warnungen nach Bedarf für den neuen Wiederherstellungsserver

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur [Geschäftskontinuität](concepts-business-continuity.md)

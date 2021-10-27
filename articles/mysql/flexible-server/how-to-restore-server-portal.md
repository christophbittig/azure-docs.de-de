---
title: Wiederherstellen einer Instanz von Azure Database for MySQL Flexible Server über das Azure-Portal
description: In diesem Artikel wird beschrieben, wie Sie Wiederherstellungsvorgänge für Azure Database for MySQL Flexible Server über das Azure-Portal durchführen.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 20632b46067f7ec1db6add26d9bed4318f6a7fd5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069613"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Zeitpunktwiederherstellung einer Instanz von Azure Database for MySQL – Flexible Server (Vorschau) über das Azure-Portal

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

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

## <a name="using-restore-to-move-a-server-from-public-access-to-private-access"></a>Ändern des Zugriffs auf einen Server von „Öffentlich“ in „Privat“ mithilfe der Wiederherstellung

Führen Sie die folgenden Schritte aus, um Ihre Flexible Server-Instanz mit der frühesten vorhandenen Sicherung wiederherzustellen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihre Flexible Server-Instanz aus, von der aus Sie die Sicherung wiederherstellen möchten.

2. Klicken Sie auf der Seite „Übersicht“ auf **Wiederherstellen**.

3. Die Wiederherstellungsseite wird angezeigt, und Sie können zwischen „Frühester Wiederherstellungspunkt“ und „Benutzerdefinierter Wiederherstellungspunkt“ auswählen.

4. Wählen Sie entweder **Frühester Wiederherstellungspunkt** oder einen **benutzerdefinierten Wiederherstellungspunkt**.

5. Geben Sie einen neuen Servernamen im Feld **Auf neuem Server wiederherstellen** ein.

6. Geben Sie einen neuen Servernamen im Feld **Auf neuem Server wiederherstellen** ein.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-private-dns-zone.png" alt-text="Übersicht anzeigen":::

7. Wechseln Sie zur Registerkarte **Netzwerk**, um Netzwerkeinstellungen festzulegen.

8. Wählen Sie unter **Konnektivitätsmethode** die Option **Privater Zugriff (VNET-Integration)** aus. Wechseln Sie zum Abschnitt **Virtuelles Netzwerk**. Sie können entweder ein bereits vorhandenes *virtuelles Netzwerk* und *Subnetz* auswählen, das an *Microsoft.DBforMySQL/flexibleServers* delegiert ist, oder ein neues erstellen, indem Sie auf den Link *Virtuelles Netzwerk erstellen* klicken.
    > [!Note]
    > In der Dropdownliste werden nur virtuelle Netze und Subnetze in derselben Region und im selben Abonnement angezeigt. </br>
    > Das ausgewählte Subnetz wird an *Microsoft.DBforMySQL/flexibleServers* delegiert. Dies bedeutet, dass dieses Subnetz nur von flexiblen Azure Database for MySQL-Servern genutzt werden kann.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="VNet-Konfiguration":::

9. Erstellen Sie eine neue oder wählen Sie eine vorhandene **private DNS-Zone** aus.
    > [!NOTE]
    > Namen privater DNS-Zonen müssen mit `mysql.database.azure.com` enden. </br>
    > Wenn die Option zum Erstellen einer neuen privaten DNS-Zone nicht angezeigt wird, geben Sie den Servernamen auf der Registerkarte **Grundlagen** ein.</br>
    > Nachdem der flexible Server in einem virtuellen Netzwerk und Subnetz bereitgestellt wurde, können Sie ihn nicht mehr in „Öffentlicher Zugriff (zugelassene IP-Adressen)“ verschieben.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="DNS-Konfiguration":::
10. Wählen Sie **Überprüfen + erstellen** aus, um Ihre Flexible Server-Konfiguration zu überprüfen.
11. Wählen Sie **Erstellen** aus, um den Server bereitzustellen. Die Bereitstellung kann einige Minuten dauern.

12. Es wird eine Benachrichtigung angezeigt, dass der Wiederherstellungsvorgang eingeleitet wurde.

## <a name="perform-post-restore-tasks"></a>Durchführen der Aufgaben nach der Wiederherstellung

Nachdem die Wiederherstellung abgeschlossen ist, sollten Sie die folgenden Aufgaben durchführen, um Ihre Benutzer und Anwendungen wieder in den betriebsbereiten Zustand zu versetzen:

- Umleiten von Clients und Clientanwendungen an den neuen Server, wenn der neue Server den ursprünglichen Server ersetzen soll.
- Stellen Sie sicher, dass geeignete VNET-Regeln vorhanden sind, damit Benutzer eine Verbindung herstellen können. Diese Regeln werden nicht vom ursprünglichen Server kopiert.
- Sicherstellen, dass geeignete Anmeldungen und Berechtigungen auf Datenbankebene vorhanden sind.
- Konfigurieren von Warnungen nach Bedarf für den neuen Wiederherstellungsserver

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur [Geschäftskontinuität](concepts-business-continuity.md)

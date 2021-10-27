---
title: Privaten Zugriff aktivieren (Vorschau) – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Einrichten eines privaten Links in einer Servergruppe für Azure Database for PostgreSQL – Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: e7694cd55eb6b6da7adb0313de47575880854cc5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075040"
---
# <a name="private-access-preview-in-azure-database-for-postgresql-hyperscale-citus"></a>Privater Zugriff (Vorschau) in Azure PostgreSQL Database Hyperscale (Citus)

Mit [privatem Zugriff](concepts-hyperscale-private-access.md) (Vorschau) können Ressourcen in einem virtuellen Azure-Netzwerk eine sichere und private Verbindung mit Knoten in einer Hyperscale (Citus)-Servergruppe herstellen. In dieser Anleitung wird davon ausgegangen, dass Sie bereits ein virtuelles Netzwerk und ein Subnetz erstellt haben. Ein Beispiel für die Einrichtung von Voraussetzungen finden Sie im [Tutorial zum privaten Zugriff](tutorial-hyperscale-private-access.md).

## <a name="create-a-server-group-with-a-private-endpoint"></a>Erstellen einer Servergruppe mit einem privaten Endpunkt

1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen**.

2. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann auf der Seite **Datenbanken** die Option **Azure-Datenbank für PostgreSQL** aus.

3. Wählen Sie für die Bereitstellungsoption unter **Hyperscale (Citus)-Servergruppe** die Schaltfläche **Erstellen** aus.

4. Füllen Sie das neue Serverdetailsformular mit Ihrer Ressourcengruppe, dem gewünschten Servergruppennamen, dem Speicherort und dem Kennwort des Datenbankbenutzers aus.

5. Wählen Sie **Servergruppe konfigurieren** und dann den gewünschten Plan aus. Klicken Sie anschließend auf **Speichern**.

6. Klicken Sie auf **Weiter: Netzwerk** aus (im unteren Bereich der Seite).

7. Wählen Sie **Privater Zugriff (Vorschau)** aus.

    > [!NOTE]
    >
    > Privater Zugriff ist nur in [bestimmten Regionen](concepts-hyperscale-limits.md#regions) als Vorschauversion verfügbar.
    >
    > Wenn die Option „Privater Zugriff“ für Ihre Servergruppe nicht ausgewählt werden kann, obwohl sich Ihre Servergruppe in einer zulässigen Region befindet, öffnen Sie eine [Azure-Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), und geben Sie Ihre Azure-Abonnement-ID ein, um Zugriff zu erhalten.

8. Ein Bildschirm mit dem Namen **Privaten Endpunkt erstellen** wird angezeigt. Wählen Sie die entsprechenden Werte für Ihre vorhandenen Ressourcen aus, und klicken Sie auf **OK**:

    - **Ressourcengruppe**
    - **Location**
    - **Name**
    - **Zielunterressource**
    - **Virtuelles Netzwerk**
    - **Subnetz**
    - **Integration in eine private DNS-Zone**

9. Wählen Sie nach dem Erstellen des privaten Endpunkts **Überprüfen und erstellen** aus, um Ihre Hyperscale (Citus)-Servergruppe zu erstellen.

## <a name="enable-private-access-on-an-existing-server-group"></a>Aktivieren des privaten Zugriffs auf eine vorhandene Servergruppe

Um einen privaten Endpunkt für einen Knoten in einer vorhandenen Servergruppe zu erstellen, öffnen Sie die Seite **Netzwerk** für die Servergruppe.

1. Wählen Sie **Privaten Endpunkt hinzufügen**.

   :::image type="content" source="media/howto-hyperscale-private-access/networking.png" alt-text="Bildschirm „Netzwerk“":::

1. Bestätigen Sie das **Abonnement**, die **Ressourcengruppe** und die **Region** in der Registerkarte **Grundlagen**. Geben Sie einen **Namen** für den Endpunkt ein, beispielsweise `my-server-group-eq`.

    > [!NOTE]
    >
    > Es wird empfohlen, ein Abonnement und eine Region auszuwählen, die mit denen Ihrer Servergruppe übereinstimmen.  Die Standardwerte für die Formularfelder sind möglicherweise nicht korrekt. Überprüfen Sie sie, und aktualisieren Sie sie bei Bedarf.

2. Wählen Sie **Weiter: Ressource >** aus. Wählen Sie in der **Zielunterressource** den Zielknoten der Servergruppe aus. Im Allgemeinen ist `coordinator` der gewünschte Knoten.

3. Klicken Sie auf **Weiter: Konfiguration >** . Wählen Sie das gewünschte **virtuelle Netzwerk** und ein **Subnetz** aus. Passen Sie die **Integration des privaten DNS** an, oder übernehmen Sie die Standardeinstellungen.

4. Wählen Sie **Weiter: Tags >** aus, und fügen Sie alle gewünschten Tags hinzu.

5. Wählen Sie schließlich **Überprüfen und erstellen** aus. Überprüfen Sie die Einstellungen, und wählen Sie **Erstellen** aus, wenn Sie zufrieden sind.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [privatem Zugriff](concepts-hyperscale-private-access.md) (Vorschau).
* Folgen Sie einem [Tutorial](tutorial-hyperscale-private-access.md), um den privaten Zugriff (Vorschau) in Aktion zu sehen.

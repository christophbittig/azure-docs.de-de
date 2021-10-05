---
title: Migrieren einer V2-Azure IoT Central-Anwendung zu V3 | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre V2-Azure IoT Central-Anwendung zu V3 migrieren.
author: troyhopwood
ms.author: troyhop
ms.date: 09/17/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: contperf-fy22q1
ms.openlocfilehash: 6a7a0d8f6fe2b057baf1fa568700b4139ec31398
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660897"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>Migrieren Ihrer V2-IoT Central-Anwendung zu V3

Wenn Sie zurzeit eine neue IoT Central-Anwendung erstellen, ist dies eine V3-Anwendung. Wenn Sie früher eine Anwendung erstellt haben, ist sie – je nach dem Zeitpunkt ihrer Erstellung – möglicherweise V2. In diesem Artikel wird beschrieben, wie Sie eine V2- zu einer V3-Anwendung migrieren, um sicherzustellen, dass Sie die neuesten IoT Central-Features verwenden.

Weitere Informationen finden Sie in der [Ankündigung zur Außerbetriebnahme](/answers/questions/529295/retirement-announcement-upgrade-to-iot-central-v3.html).

Wenn Sie erfahren möchten, wie Sie die Version einer IoT Central-Anwendung identifizieren können, lesen Sie [Wie kann ich Informationen zu meiner Anwendung erhalten?](howto-faq.yml#how-do-i-get-information-about-my-application-).

Führen Sie zum Migrieren einer Anwendung von V2 zu V3 folgende Schritte aus:

1. Erstellen Sie aus der V2-Anwendung eine neue V3-Anwendung.
1. Konfigurieren Sie die V3-Anwendung.
1. Löschen Sie die V2-Anwendung.

## <a name="create-a-new-v3-application"></a>Erstellen einer neuen V3-Anwendung

IoT Central unterstützt nicht die Migration zu einer vorhandenen V3-Anwendung. Um vorhandene Geräte von einer V2- in eine V3-Anwendung zu verschieben, verwenden Sie den Migrationsassistenten, um Ihre V3-Anwendung zu erstellen.

Der Migrations-Assistent führt folgende Aktionen aus:

- Er erstellt eine neue V3-Anwendung.
- Er überprüft Ihre Gerätevorlagen auf Kompatibilität mit V3.
- Er kopiert alle Ihre Gerätevorlagen in die neue V3-Anwendung.
- Er kopiert alle Benutzer und Rollenzuweisungen in die neue V3-Anwendung.

> [!NOTE]
> Zur Sicherstellung der Gerätekompatibilität mit V3 werden aus primitiven Typen in der Gerätevorlage Objekteigenschaften. Sie müssen an Ihrem Gerätecode keine Änderungen vornehmen.

Zum Migrieren einer Anwendung zu V3 müssen Sie ein Administrator sein.

1. Wählen Sie im Menü **Verwaltung** die Option **Migrate to a V3 application**  (Zu einer V3-Anwendung migrieren) aus:

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="Screenshot des Anwendungsmigrations-Assistenten":::

1. Geben Sie einen neuen **Anwendungsname** n ein, und ändern Sie optional die automatisch generierte **URL**. Die URL darf mit der URL Ihrer aktuellen V2-Anwendung nicht identisch sein. Sie können die URL aber später nach dem Löschen der V2-Anwendung ändern.

1. Wählen Sie **Create a new V3 app** (Neue V3-App erstellen) aus.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="Screenshot der Optionen im Anwendungsmigrations-Assistenten":::

    Je nach der Anzahl und Komplexität Ihrer Gerätevorlagen kann es einige Minuten dauern, bis dieser Vorgang abgeschlossen ist.

    > [!Warning]
    > Die Erstellung Ihrer V3-Anwendung schlägt fehl, wenn eine Gerätevorlage Felder enthält, die mit einer Zahl beginnen oder eines der folgenden Zeichen enthalten (`+`, `*`, `?`, `^`, `$`, `(`, `)`, `[`, `]`, `{`, `}`, `|`, `\`). Das von diesen V3-Anwendungen verwendete DTDL-Gerätevorlagenschema lässt diese Zeichen nicht zu. Aktualisieren Sie Ihre Gerätevorlage zur Behebung dieses Problems, bevor Sie zu V3 migrieren.

1. Wenn Ihre neue V3-App bereit ist, wählen Sie **Ihre neue App öffnen** aus, um sie zu öffnen.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="Screenshot des Anwendungsmigrations-Assistenten nach der Anwendungsmigration":::

## <a name="configure-the-v3-application"></a>Konfigurieren der V3-Anwendung

Nachdem Ihre neue V3-Anwendung erstellt wurde, nehmen Sie alle Konfigurationsänderungen vor, bevor Sie Ihre Geräte aus der V2-Anwendung in die V3-Anwendung verschieben.

> [!TIP]
> Nehmen Sie sich einen Moment Zeit, um [sich mit V3 vertraut zu machen](overview-iot-central-tour.md#navigate-your-application), da es hier einige Unterschiede zur vorherigen Version V2 gibt.

Hier sind einige empfohlene Konfigurationsschritte, die zu beachten sind:

- [Konfigurieren von Dashboards](howto-manage-dashboards.md)
- [Konfigurieren des Datenexports](howto-export-data.md)
- [Konfigurieren von Regeln und Aktionen für Ihr Gerät in Azure IoT Central (Previewfunktionen)](quick-configure-rules.md)
- [Anpassen der Anwendungsbenutzeroberfläche](howto-customize-ui.md)

Wenn Ihre V3-Anwendung Ihren Anforderungen entsprechend konfiguriert wurde, können Sie Ihre Geräte aus Ihrer V2-Anwendung in Ihre V3-Anwendung verschieben.

## <a name="move-your-devices-to-the-v3-application"></a>Verschieben Ihrer Geräte in die V3-Anwendung

Nach Abschluss dieses Schritts kommunizieren alle Ihre Geräte nur mit Ihrer neuen V3-Anwendung.

> [!IMPORTANT]
> Bevor Sie Ihre Geräte in Ihre V3-Anwendung verschieben, löschen Sie alle Geräte, die Sie in der V3-Anwendung erstellt haben.

Mit diesem Schritt werden alle Ihre vorhandenen Geräte in Ihre neue V3-Anwendung verschoben. Ihre Gerätedaten werden nicht kopiert.

Wählen Sie **Alle Geräte verschieben** aus, um mit dem Verschieben Ihrer Geräte zu beginnen. Dieser Schritt könnte einige Minuten bis zum Abschluss dauern.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="Screenshot des Anwendungsmigrations-Assistenten mit der Option „Geräte verschieben“":::

Starten Sie nach Abschluss des Verschiebevorgangs alle Ihre Geräte neu, um sicherzustellen, dass sie eine Verbindung mit der neuen V3-Anwendung herstellen.

> [!WARNING]
> Löschen Sie Ihre V3-Anwendung nicht, weil Ihre V2-Anwendung jetzt nicht mehr funktionsfähig ist.

## <a name="delete-your-old-v2-application"></a>Löschen Ihrer alten V2-Anwendung

Nachdem Sie überprüft haben, ob in Ihrer neuen V3-Anwendung alles erwartungsgemäß funktioniert, löschen Sie die alte V2-Anwendung. Mit diesem Schritt wird sichergestellt, dass Ihnen eine nicht mehr genutzte Anwendung nicht in Rechnung gestellt wird.

> [!Note]
> Zum Löschen einer Anwendung benötigen Sie Berechtigungen zum Löschen von Ressourcen in dem Azure-Abonnement, das Sie beim Erstellen der Anwendung gewählt haben. Weitere Informationen finden Sie unter [Verwalten des Zugriffs mithilfe der RBAC und des Azure-Portals](../../role-based-access-control/role-assignments-portal.md).

1. Wählen Sie in Ihrer V2-Anwendung im Menü die Registerkarte **Verwaltung** aus.
2. Wählen Sie **Löschen** aus, um Ihre IoT Central-Anwendung dauerhaft zu löschen. Bei dieser Option werden alle dieser Anwendung zugeordneten Daten endgültig gelöscht.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Ihre Anwendung migrieren, sollten Sie sich im nächsten Schritt in der [Azure IoT Central-Benutzeroberfläche](overview-iot-central-tour.md) ansehen, was sich in V3 geändert hat.

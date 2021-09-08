---
title: Erstellen eines Serverendpunkts für die Azure-Dateisynchronisierung
description: Machen Sie sich mit den Optionen während der Erstellung des Serverendpunkts und deren optimaler Anwendung auf Ihre Situation klar.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 06/01/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: b981ae7394e070d4da793fddccdd52e4350bde86
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799124"
---
# <a name="create-an-azure-file-sync-server-endpoint"></a>Erstellen eines Serverendpunkts für die Azure-Dateisynchronisierung

Ein Serverendpunkt stellt einen bestimmten Speicherort auf einem registrierten Server dar, z. B. einen Ordner auf einem Servervolume. Ein Serverendpunkt muss die folgenden Bedingungen erfüllen:

- Ein Serverendpunkt muss ein Pfad auf einem registrierten Server sein (statt einer eingebundenen Freigabe). Network Attached Storage (NAS) wird nicht unterstützt.
- Der Serverendpunkt kann sich zwar auf dem Systemvolumen befinden, Serverendpunkte auf dem Systemvolumen verwenden jedoch möglicherweise kein Cloudtiering.
- Das Ändern des Pfads oder Laufwerkbuchstabens nachdem Sie einen Serverendpunkt auf einem Volume erstellt haben, wird nicht unterstützt. Stellen Sie sicher, dass Sie einen geeigneten Pfad verwenden, bevor Sie den Serverendpunkt erstellen.
- Ein registrierter Server kann mehrere Serverendpunkte unterstützen, jedoch kann eine Synchronisierungsgruppe pro registriertem Server immer nur einen Serverendpunkt haben. Andere Serverendpunkte innerhalb der Synchronisierungsgruppe müssen sich auf unterschiedlichen registrierten Servern befinden.
- Mehrere Serverendpunkte können auf dem gleichen Volume vorhanden sein, wenn sich deren Namespaces nicht überschneiden (z. B. „F:\sync1“ und „F:\sync2“) und jeder Endpunkt mit einer eindeutigen Synchronisierungsgruppe synchronisiert wird. 

In diesem Artikel werden die Optionen und Entscheidungen beschrieben, die zum Erstellen eines neuen Serverendpunkts und zum Starten der Synchronisierung erforderlich sind. Damit dies funktioniert, müssen Sie die [Planung für Ihre Bereitstellung der Azure-Dateisynchronisierung](file-sync-planning.md) abgeschlossen und auch [in vorherigen Schritten benötigte Ressourcen](file-sync-deployment-guide.md) bereitgestellt haben, um einen Serverendpunkt zu erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Erstellen eines Serverendpunkts müssen Sie zunächst sicherstellen, dass die folgenden Kriterien erfüllt sind: 
- Auf dem Server ist der Azure-Dateisynchronisierungs-Agent installiert und registriert. Näheres zum Installieren des Azure-Dateisynchronisierungs-Agents finden Sie unter [Verwalten registrierter Server mit der Azure-Dateisynchronisierung](file-sync-server-registration.md). 
- Stellen Sie sicher, dass der Speichersynchronisierungsdienst bereitgestellt wurde. Ausführliche Informationen zum Bereitstellen eines Speichersynchronisierungsdiensts finden Sie unter [Bereitstellen der Azure-Dateisynchronisierung (Vorschau)](file-sync-deployment-guide.md). 
- Stellen Sie sicher, dass eine Synchronisierungsgruppe bereitgestellt wurde. Erfahren Sie, wie Sie eine [Synchronisierungsgruppe erstellen](file-sync-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint).
- Stellen Sie sicher, dass der Server mit dem Internet verbunden ist und dass der Zugriff auf Azure möglich ist. Azure-Dateisynchronisierung verwendet Port 443 für die gesamte Kommunikation zwischen dem Server und dem Clouddienst.

## <a name="create-a-server-endpoint"></a>Erstellen eines Serverendpunkts

[!INCLUDE [storage-files-sync-create-server-endpoint](../../../includes/storage-files-sync-create-server-endpoint.md)]

### <a name="cloud-tiering-section"></a>Cloudtieringabschnitt

Wenn Sie einen neuen Serverendpunkt erstellen, können Sie das Cloudtieringfeature der Azure-Dateisynchronisierung nutzen. Die Optionen im Abschnitt **Cloudtiering** können später geändert werden. Im folgenden Abschnitt stehen jedoch verschiedene Optionen zur Verfügung, je nachdem, ob Cloudtiering für Ihren neuen Serverendpunkt aktiviert ist oder nicht.

Lesen Sie den [Artikel zum Cloudtiering](file-sync-cloud-tiering-overview.md), in dem die Grundlagen, die [Richtlinien](file-sync-cloud-tiering-policy.md) und bewährten Methoden ausführlich behandelt werden. 

### <a name="initial-sync-section"></a>Abschnitt „Erste Synchronisierung“

Der Abschnitt **Erste Synchronisierung** ist nur für den ersten Serverendpunkt in einer Synchronisierungsgruppe verfügbar. Informationen zu weiteren Serverendpunkten finden Sie im [Abschnitt zum anfänglichen Download](#initial-download-section).

Es gibt zwei grundlegend unterschiedliche Verhalten bei der ersten Synchronisierung:

:::row:::
    :::column:::
        **Zusammenführen** 
    :::column-end:::
    :::column:::
        **Autoritativer Upload**
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        „Zusammenführen“ ist die standardmäßig ausgewählte Option. Sie sollten die Auswahl **Zusammenführen** beibehalten, sofern bestimmte Migrationsszenarien nichts anderes erfordern. 
* Wenn Sie einen Serverstandort einbinden, ist in den meisten Szenarien entweder der Serverstandort oder die Cloudfreigabe leer. In diesen Fällen ist **Zusammenführen** das richtige Verhalten und führt zu erwarteten Ergebnissen. 
* Wenn beide Speicherorte Dateien und Ordner enthalten, werden die Namespaces zusammengeführt. Wenn Dateien oder Ordnernamen auf dem Server vorhanden sind, die auch in der Cloudfreigabe vorhanden sind, kommt es zu einem Synchronisierungskonflikt. [Konflikte werden automatisch gelöst](../files/storage-files-faq.md#afs-conflict-resolution). </br> </br> Innerhalb der Option **Zusammenführen** muss eine Auswahl getroffen werden darüber, wie Inhalte aus der Azure-Dateifreigabe anfänglich auf dem Server eintreffen. Diese Auswahl hat keine Auswirkungen, wenn die Azure-Dateifreigabe leer ist. Weitere Informationen finden Sie im kommenden Absatz: [Anfänglicher Download](#initial-download-section)
    :::column-end:::
    :::column:::
        **Autoritativer Upload** ist eine Option für die erste Synchronisierung, die für ein bestimmtes Migrationsszenario reserviert ist. Synchronisieren desselben Serverpfads, der auch zum Seeding der Cloudfreigabe verwendet wurde, z. B. mit Azure Data Box. In diesem Fall verfügen die Cloud und die Serverstandorte größtenteils über die gleichen Daten, aber der Server ist etwas neuer. Benutzer haben während des Transports der Data Box weiterhin Änderungen vorgenommen. Dieses Migrationsszenario erfordert dann eine nahtlose Aktualisierung der Cloud mit den Änderungen auf dem Server (neuer), ohne dass Konflikte entstehen. Daher ist der Server die Autorität der Form des Namespace, und Data Box wurde verwendet, um einen großen anfänglichen Upload vom Server zu vermeiden. Der autoritative Upload des Servers ermöglicht auch dann eine Cloudeinführung ohne Ausfallzeiten, wenn ein Mechanismus zum Offlinedatentransport verwendet wurde, um ein Seeding des Cloudspeichers auszuführen.
    :::column-end:::
:::row-end:::

Die Bereitstellung eines Serverendpunkts kann nur dann mit der autoritativen Uploadoption erfolgreich ausgeführt werden, wenn darin Daten des Serverstandorts enthalten sind. Dieser Block soll vor versehentlichen Fehlkonfigurationen schützen. Autoritativer Upload funktioniert wie RoboCopy /MIR. Dieser Modus spiegelt die Quelle im Ziel. Die Quelle ist der AFS-Server und das Ziel die Cloudfreigabe. Autoritativer Upload formt das Ziel im Image der Quelle. 

* Neue oder aktualisierte Dateien und Ordner werden vom Server hochgeladen.
* Nicht (mehr) auf dem Server vorhandene Dateien und Ordner werden aus der Cloudfreigabe gelöscht.
* Ausschließliche Metadatenänderungen an Dateien und Ordnern auf dem Server werden effizient als ausschließliche Metadatenaktualisierungen in die Cloudfreigabe verschoben.
* Dateien und Ordner könnten auf dem Server und in der Cloudfreigabe vorhanden sein. Das übergeordnete Verzeichnis einiger Dateien oder Ordner auf dem Server hat sich jedoch möglicherweise seit dem Seeding der Azure-Dateifreigabe geändert. Diese Dateien und Ordner werden aus der Cloudfreigabe gelöscht und erneut hochgeladen. Aus diesem Grund sollten Sie während einer Migration das Umstrukturieren Ihres Namespace in größerem Umfang vermeiden.

### <a name="initial-download-section"></a>Abschnitt „Anfänglicher Download“

Der Abschnitt **Anfänglicher Download** ist für den zweiten und alle anderen Serverendpunkte in einer Synchronisierungsgruppe verfügbar. Der [erste Serverendpunkt in einer Synchronisierungsgruppe verfügt über zusätzliche Optionen](#initial-sync-section), die sich auf die Migration mit Azure Data Box beziehen. Diese Optionen gelten nicht, wenn dieser Serverendpunkt nicht der erste in Ihrer Synchronisierungsgruppe ist.

> [!NOTE]
> Die Auswahl einer Option für den anfänglichen Download hat keine Auswirkungen, wenn die Azure-Dateifreigabe leer ist.

Im Rahmen dieses Abschnitts können Sie entscheiden, wie Inhalte aus der Azure-Dateifreigabe anfänglich auf dem Server eintreffen:

:::row:::
    :::column:::
        :::image type="content" source="media/file-sync-server-endpoint-create/initial-download-options.png" alt-text="Ein Bild, das die Optionen im Assistenten zum Erstellen von Serverendpunkten im Azure-Portal veranschaulicht." lightbox="media/file-sync-server-endpoint-create/initial-download-options-expanded.png":::
    :::column-end:::
    :::column:::
* **Nur Namespace** </br> Es wird nur die Datei- und Ordnerstruktur aus der Azure-Dateifreigabe auf den lokalen Server übertragen. Kein Dateiinhalt wird heruntergeladen. Diese Option ist der Standard, wenn Sie zuvor Cloudtiering für diesen neuen Serverendpunkt aktiviert haben.
* **Namespace zuerst, dann Inhalt** </br> Für eine schnellere Verfügbarkeit der Daten wird Ihr Namespace unabhängig von Ihrer Cloudtieringeinstellung zuerst heruntergefahren. Sobald der Namespace auf dem Server verfügbar ist, wird der Dateiinhalt aus der Cloud auf den Server abgerufen. Der Abruf erfolgt basierend auf dem Zeitstempel der letzten Änderung der jeweiligen Datei. Wenn auf dem Servervolume nicht genügend Speicherplatz vorhanden ist, bleiben die verbleibenden Dateien mehrstufige Dateien. Diese Option ist der Standard, wenn Sie das Cloudtiering für diesen Serverendpunkt nicht aktiviert haben.
* **Vermeiden von mehrstufigen Dateien** </br> Mit dieser Option wird jede Datei vollständig heruntergeladen, bevor sie im Ordner auf dem Server angezeigt wird. Mit dieser Option wird vermieden, dass jemals eine mehrstufige Datei auf dem Server vorhanden ist. Ein Namespaceelement und Dateiinhalt sind immer gleichzeitig vorhanden. Vermeiden Sie diese Option, wenn Sie einen Serverendpunkt für die schnelle Notfallwiederherstellung aus der Cloud erstellen. Wenn Sie Anwendungen haben, für die vollständige Dateien vorhanden sein müssen und mehrstufige Dateien in ihrem Namespace nicht akzeptabel sind, ist diese Lösung ideal. Diese Option ist nicht verfügbar, wenn Sie Cloudtiering für Ihren neuen Serverendpunkt verwenden.
    :::column-end:::
:::row-end:::

Nachdem Sie eine Option für den anfänglichen Download ausgewählt haben, können Sie sie nicht mehr ändern, nachdem Sie bestätigt haben, dass der Serverendpunkt erstellt werden soll. Wie Dateien nach Abschluss des anfänglichen Downloads auf dem Server angezeigt werden, hängt von Ihrer Verwendung des Cloudtieringfeatures ab und davon, ob Sie sich für das [proaktive Abrufen von Änderungen in der Cloud entschieden haben](file-sync-cloud-tiering-overview.md#proactive-recalling). Das letztgenannte Feature ist für Synchronisierungsgruppen mit mehreren Serverendpunkten an verschiedenen geografischen Standorten nützlich.

* **Cloudtiering ist aktiviert** </br> Neue und geänderte Dateien von anderen Serverendpunkten werden auf diesem Serverendpunkt als mehrstufige Dateien angezeigt. Diese Änderungen werden nur dann als vollständige Dateien angezeigt, wenn Sie sich für den [proaktiven Abruf](file-sync-cloud-tiering-overview.md#proactive-recalling) von Änderungen in der Azure-Dateifreigabe durch andere Serverendpunkte entschieden haben.
*  **Cloudtiering ist deaktiviert** </br> Neue und geänderte Dateien von anderen Serverendpunkten werden auf diesem Serverendpunkt als mehrstufige Dateien angezeigt. Sie werden nicht zuerst als mehrstufige Dateien angezeigt und dann abgerufen. Mehrstufige Dateien mit deaktiviertem Cloudtiering sind ein Feature für die schnelle Notfallwiederherstellung und werden nur bei der ersten Bereitstellung angezeigt.


## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich weiter mit Azure-Dateifreigaben und der Azure-Dateisynchronisierung vertraut. In den folgenden Artikeln werden erweiterte Optionen und bewährte Methoden behandelt. Sie bieten auch Hilfestellung bei der Problembehandlung. Diese Artikel enthalten gegebenenfalls Links zur [Dokumentation für Azure-Dateifreigaben](../files/storage-files-introduction.md).

* [Migration overview (Übersicht über die Migration)](../files/storage-files-migration-overview.md)
* [Planung für die Bereitstellung der Azure-Dateisynchronisierung](../file-sync/file-sync-planning.md)
* [Erstellen einer Dateifreigabe](../files/storage-how-to-create-file-share.md)
* [Problembehandlung für die Azure-Dateisynchronisierung](../file-sync/file-sync-troubleshoot.md)

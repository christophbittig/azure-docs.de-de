---
title: Problembehandlung für Azure Data Factory Studio
description: Hier erfahren Sie, wie Sie Probleme bei Azure Data Factory Studio beheben können.
author: ceespino
ms.service: data-factory
ms.subservice: authoring
ms.topic: troubleshooting
ms.date: 06/01/2021
ms.author: ceespino
ms.reviewer: susabat
ms.openlocfilehash: fda5131d4a4578de01dc5a34aada295c6b364848
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663134"
---
# <a name="troubleshoot-azure-data-factory-studio-issues"></a>Problembehandlung für Azure Data Factory Studio

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden die gängigen Methoden für die Problembehandlung bei [Azure Data Factory Studio](https://adf.azure.com) beschrieben, der Benutzeroberfläche des ADF-Diensts.

## <a name="azure-data-factory-studio-fails-to-load"></a>Fehler beim Laden von Azure Data Factory Studio

> [!NOTE]
> Azure Data Factory Studio unterstützt offiziell Microsoft Edge und Google Chrome. Die Verwendung anderer Webbrowser kann zu unerwartetem oder nicht dokumentiertem Verhalten führen.

### <a name="third-party-cookies-blocked"></a>Drittanbietercookies blockiert

In Azure Data Factory Studio werden Browsercookies verwendet, um den Status der Benutzersitzung dauerhaft zu speichern sowie interaktive Entwicklungs- und Überwachungsumgebungen zu aktivieren. Möglicherweise blockiert Ihr Browser Drittanbietercookies, da Sie eine Inkognitositzung verwenden oder einen Werbeblocker aktiviert haben. Das Blockieren von Drittanbietercookies kann beim Laden des Portals zu Problemen führen.  Sie könnten zu einer leeren Seite oder zu „https://adf.azure.com/accesstoken.html“ umgeleitet werden, oder es könnte eine Warnmeldung mit dem Hinweis angezeigt werden, dass Drittanbietercookies blockiert werden. Aktivieren Sie zur Behebung dieses Problems die Optionen für Drittanbietercookies in Ihrem Browser mit den folgenden Schritten:

# <a name="microsoft-edge"></a>[Microsoft Edge](#tab/edge)

#### <a name="allow-all-cookies"></a>Alle Cookies zulassen

1. Navigieren Sie in Ihrem Browser zu **edge://settings/content/cookies**.
1. Stellen Sie sicher, dass **Allow sites to save and read cookie data** (Für Websites das Speichern und Lesen von Cookiedaten zulassen) aktiviert und die Option **Cookies von Drittanbietern blockieren** deaktiviert ist. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png" alt-text="Alle Cookies in Microsoft Edge zulassen":::
1. Aktualisieren Sie Azure Data Factory Studio, und versuchen Sie es erneut.

#### <a name="only-allow-azure-data-factory-studio-to-use-cookies"></a>Zulassen von Cookies nur für Azure Data Factory Studio

Wenn Sie nicht alle Cookies zulassen möchten, können Sie diese optional nur für ADF Studio zulassen:

1. Navigieren Sie zu **edge://settings/content/cookies**.
1. Wählen Sie im Abschnitt **Zulassen** die Option **Hinzufügen** aus, und fügen Sie die Website **adf.azure.com** hinzu. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png" alt-text="Hinzufügen der ADF-Benutzeroberfläche zu zugelassenen Websites in Microsoft Edge":::
1. Aktualisieren Sie die ADF-Benutzeroberfläche, und versuchen Sie es erneut.

# <a name="google-chrome"></a>[Google Chrome](#tab/chrome)

#### <a name="allow-all-cookies"></a>Alle Cookies zulassen

1. Navigieren Sie in Ihrem Browser zu **chrome://settings/cookies**.
1. Wählen Sie die Option **Alle Cookies zulassen** aus. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png" alt-text="Alle Cookies in Chrome zulassen":::
1. Aktualisieren Sie Azure Data Factory Studio, und versuchen Sie es erneut.

#### <a name="only-allow-azure-data-factory-studio-to-use-cookies"></a>Zulassen von Cookies nur für Azure Data Factory Studio

Wenn Sie nicht alle Cookies zulassen möchten, können Sie diese optional nur für ADF Studio zulassen:
1. Navigieren Sie zu **chrome://settings/cookies**.
1. Wählen Sie unter der Option **Sites that can always use cookies** (Websites, die immer Cookies verwenden können) **Hinzufügen** aus. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png" alt-text="Hinzufügen der ADF-Benutzeroberfläche zu zugelassenen Websites in Chrome":::
1. Fügen Sie die Website **adf.azure.com** hinzu, aktivieren Sie die Option **Alle Cookies**, und speichern Sie. 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png" alt-text="Alle Cookies von der Website der ADF-Benutzeroberfläche zulassen":::
1. Aktualisieren Sie die ADF-Benutzeroberfläche, und versuchen Sie es erneut.

---

## <a name="connection-failed-error-in-azure-data-factory-studio"></a>Fehler beim Herstellen einer Verbindung mit Azure Data Factory Studio

Wenn Sie beispielsweise auf **Verbindung testen** oder **Vorschau** klicken, wird in Azure Data Factory Studio möglicherweise eine ähnliche Fehlermeldung wie die folgende angezeigt. Dies bedeutet, dass beim Herstellen einer Verbindung von Ihrem lokalen Computer mit dem ADF-Dienst ein Fehler aufgetreten ist.

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/connection-failed.png" alt-text="Fehler bei der Verbindung":::

Um das Problem zu beheben, können Sie zunächst versuchen, den gleichen Vorgang in Ihrem Browser im InPrivate-Browsermodus auszuführen.

Wenn das dann immer noch nicht funktioniert, suchen Sie in der Fehlermeldung den **Servernamen** (in diesem Beispiel **dpnortheurope.svc.datafactory.azure.com**), und geben Sie dann den **Servernamen** direkt in die Adressleiste Ihres Browsers ein. 

- Wenn im Browser der Fehlercode 404 angezeigt wird, bedeutet dies in der Regel, dass Ihre Clientseite in Ordnung ist und das Problem auf der ADF-Dienstseite vorliegt. Übermitteln Sie ein Supportticket mit der **Aktivitäts-ID** aus der Fehlermeldung.

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/status-code-404.png" alt-text="Ressource nicht gefunden":::

- Wenn nicht der Fehlercode 404 angezeigt wird oder unten im Browser ein ähnlicher Fehler angezeigt wird, bedeutet dies in der Regel, dass ein clientseitiges Problem vorliegt. Befolgen Sie die weiteren Schritte zur Problembehandlung.

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/client-side-error.png" alt-text="Clientseitiger Fehler":::

Öffnen Sie zur weiteren Problembehandlung eine **Eingabeaufforderung**, und geben Sie `nslookup dpnortheurope.svc.datafactory.azure.com` ein. Das Ergebnis sieht normalerweise in etwa wie folgt aus:

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/command-response-1.png" alt-text="Befehlsantwort 1":::

- Wenden Sie sich bei einer normalen DNS-Antwort (Domain Name Service) an Ihren lokalen IT-Support, um die Firewalleinstellungen zu überprüfen.  Stellen Sie sicher, dass HTTPS-Verbindungen mit diesem Hostnamen nicht blockiert werden. Wenn das Problem weiterhin besteht, erstellen Sie ein Supportticket bei ADF mit der **Aktivitäts-ID** aus der Fehlermeldung.

- Bei einer DNS-Antwort, die sich von der normalen Antwort oben unterscheidet, kann auch ein Problem mit dem DNS-Server beim Auflösen des DNS-Namens vorliegen. Eine mögliche Problemumgehung besteht darin, den DNS-Server zu ändern (z. B. in Google DNS 8.8.8.8). 

- Wenn das Problem weiterhin besteht, könnten Sie versuchen, `nslookup datafactory.azure.com` und `nslookup azure.com` auszuführen, um zu ermitteln, auf welcher Ebene die DNS-Auflösung fehlgeschlagen ist, und dann alle Informationen an Ihren lokalen IT-Support oder Ihren ISP zur Problembehandlung übermitteln. Wenn der Support der Meinung ist, dass das Problem weiterhin aufseiten von Microsoft vorliegt, übermitteln Sie ein Supportticket mit der **Aktivitäts-ID** aus der Fehlermeldung.

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/command-response-2.png" alt-text="Befehlsantwort 2":::

## <a name="change-linked-service-type-warning-message-in-datasets"></a>Warnmeldung „Ändern des verknüpften Diensttyps“ in Datasets

Wenn Sie in einer Aktivität Datasets im Dateiformat verwenden und später auf einen verknüpften Dienst verweisen möchten, der einen anderen Typ als den zuvor in der Aktivität verwendeten aufweist (z. B. Dateisystem im Gegensatz zu Azure Data Lake Storage Gen2), wird möglicherweise die folgende Warnmeldung angezeigt:

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/warning-message.png" alt-text="Warnmeldung":::

Datasets im Dateiformat können mit allen dateibasierten Connectors verwendet werden. So können Sie z. B. ein Parquet-Dataset für Azure Blob Storage oder Azure Data Lake Storage Gen2 konfigurieren. Beachten Sie, dass die einzelnen Connectors unterschiedliche datenspeicherbezogene Einstellungen für die Aktivität sowie unterschiedliche App-Modelle unterstützen. 

Wenn Sie auf der ADF-Benutzeroberfläche für die Erstellung ein Dataset im Dateiformat für eine Aktivität (einschließlich „Kopieren“, „Suchen“, „Metadaten abrufen“ und „Löschen“) verwenden und im Dataset auf einen verknüpften Dienst verweisen möchten, dessen Typ nicht dem aktuellen Typ der Aktivität entspricht (z. B. Dateisystem im Gegensatz zu ADLS Gen2), wird diese Warnmeldung angezeigt. Für einen reibungslosen Wechsel werden die Pipelines und Aktivitäten, die auf dieses Dataset verweisen, nach Ihrer Zustimmung so geändert, dass sie ebenfalls den neuen Typ verwenden. Da vorhandene Datenspeichereinstellungen, die nicht mit dem neuen Typ kompatibel sind, nicht mehr gültig sind, werden diese Einstellungen gelöscht.

Weitere Informationen zu den unterstützten Datenspeichereinstellungen für die verschiedenen Connectors finden Sie im entsprechenden Artikel zum jeweiligen Connector. Kopieren Sie die Aktivitätseigenschaften, um eine detaillierte Liste der Eigenschaften anzuzeigen. Weitere Informationen zu [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md), [Dateisystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [Hadoop Distributed File System](connector-hdfs.md), [HTTP](connector-http.md) und [SFTP](connector-sftp.md).


## <a name="could-not-load-resource-while-opening-pipeline"></a>Ressource konnte beim Öffnen der Pipeline nicht geladen werden 

Greift ein Benutzer mithilfe von Azure Data Factory Studio auf eine Pipeline zu, wird die Fehlermeldung „Fehler beim Laden der Ressource ‚xxxxxx‘“ angezeigt.  Vergewissern Sie sich, dass die JSON-Datei keine Fehler enthält und dass die Ressourcen, auf die Sie verweisen, vorhanden sind. Status: TypeError: Die Eigenschaft „xxxxx“ eines undefinierten Verweises kann nicht gelesen werden. Mögliche Ursache: TypeError: „Die Eigenschaft ‚xxxxxxx‘ eines undefinierten Verweises kann nicht gelesen werden.“

Die Quelle der Fehlermeldung ist die JSON-Datei, in der die Pipeline beschrieben wird. Dieser Fehler tritt auf, wenn der Kunde die Git-Integration verwendet und JSON-Pipelinedateien aus irgendeinem Grund beschädigt werden. Links neben dem Pipelinenamen wird ein Fehler (roter Punkt mit x) angezeigt, wie unten dargestellt.

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/pipeline-json-error.png" alt-text="JSON-Pipelinefehler":::

Die Lösung besteht darin, die Fehler in den JSON-Dateien zu korrigieren und anschließend die Pipeline mithilfe des Erstellungstools erneut zu öffnen.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

* [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
* [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-Videos](https://azure.microsoft.com/resources/videos/index/)
* [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-data-factory.html)
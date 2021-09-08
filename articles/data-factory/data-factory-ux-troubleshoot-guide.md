---
title: Problembehandlung bei der Azure Data Factory-Benutzeroberfläche
description: Hier erfahren Sie, wie Probleme bei der Azure Data Factory (ADF)-Benutzeroberfläche behandelt werden können.
author: ceespino
ms.service: data-factory
ms.subservice: authoring
ms.topic: troubleshooting
ms.date: 06/01/2021
ms.author: ceespino
ms.reviewer: susabat
ms.openlocfilehash: a6d2a05e5f461a9ccf2f5e6a3cea0ba92c461b15
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260443"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Problembehandlung bei der Azure Data Factory-Benutzeroberfläche

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden die gängigen Problembehandlungsmethoden für die Azure Data Factory (ADF)-Benutzeroberfläche beschrieben.

## <a name="adf-ux-not-loading"></a>ADF-Benutzeroberfläche wird nicht geladen

> [!NOTE]
> Die Azure Data Factory-Benutzeroberfläche unterstützt offiziell Microsoft Edge und Google Chrome. Die Verwendung anderer Webbrowser kann zu unerwartetem oder nicht dokumentiertem Verhalten führen.

### <a name="third-party-cookies-blocked"></a>Drittanbietercookies blockiert

In der ADF-Benutzeroberfläche werden Browsercookies verwendet, um die Benutzersitzung dauerhaft zu speichern sowie interaktive Entwicklungs- und Überwachungsumgebungen zu aktivieren. Es ist möglich, dass Ihr Browser Drittanbietercookies blockiert, weil Sie eine Inkognitositzung verwenden oder einen Werbeblocker aktiviert haben. Das Blockieren von Drittanbietercookies kann beim Laden des Portals zu Problemen führen. So werden Sie beispielsweise zu einer leeren Seite umgeleitet, ‚ https://adf.azure.com/accesstoken.html ‘, oder Sie erhalten eine Warnmeldung mit der Information, dass Drittanbietercookies blockiert werden. Aktivieren Sie zur Behebung dieses Problems die Optionen für Drittanbietercookies in Ihrem Browser mit den folgenden Schritten:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Alle Cookies zulassen

1. Navigieren Sie in Ihrem Browser zu **chrome://settings/cookies**.
1. Wählen Sie die Option **Alle Cookies zulassen** aus. 

    ![Alle Cookies in Chrome zulassen](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Aktualisieren Sie die ADF-Benutzeroberfläche, und versuchen Sie es erneut.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Die Verwendung von Cookies nur für die ADF-Benutzeroberfläche zulassen
Wenn Sie nicht alle Cookies zulassen möchten, können Sie sie optional nur für die ADF-Benutzeroberfläche zulassen:
1. Navigieren Sie zu **chrome://settings/cookies**.
1. Wählen Sie unter der Option **Sites that can always use cookies** (Websites, die immer Cookies verwenden können) **Hinzufügen** aus. 

    ![Hinzufügen der ADF-Benutzeroberfläche zu zugelassenen Websites in Chrome](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Fügen Sie die Website **adf.azure.com** hinzu, aktivieren Sie die Option **Alle Cookies**, und speichern Sie. 

    ![Alle Cookies von der Website der ADF-Benutzeroberfläche zulassen](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Aktualisieren Sie die ADF-Benutzeroberfläche, und versuchen Sie es erneut.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Navigieren Sie in Ihrem Browser zu **edge://settings/content/cookies**.
1. Stellen Sie sicher, dass **Allow sites to save and read cookie data** (Für Websites das Speichern und Lesen von Cookiedaten zulassen) aktiviert und die Option **Cookies von Drittanbietern blockieren** deaktiviert ist. 

    ![Alle Cookies in Microsoft Edge zulassen](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Aktualisieren Sie die ADF-Benutzeroberfläche, und versuchen Sie es erneut.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Die Verwendung von Cookies nur für die ADF-Benutzeroberfläche zulassen

Wenn Sie nicht alle Cookies zulassen möchten, können Sie sie optional nur für die ADF-Benutzeroberfläche zulassen:

1. Navigieren Sie zu **edge://settings/content/cookies**.
1. Wählen Sie im Abschnitt **Zulassen** die Option **Hinzufügen** aus, und fügen Sie die Website **adf.azure.com** hinzu. 

    ![Hinzufügen der ADF-Benutzeroberfläche zu zugelassenen Websites in Microsoft Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Aktualisieren Sie die ADF-Benutzeroberfläche, und versuchen Sie es erneut.

## <a name="connection-failed-on-adf-ux"></a>Fehler bei der Verbindung mit der ADF-Benutzeroberfläche

In manchen Fällen wird auf der ADF-Benutzeroberfläche eine Fehlermeldung vom Typ „Fehler bei Verbindung“ (wie im folgenden Screenshot) angezeigt, nachdem Sie auf **Verbindung testen**, **Vorschau** usw. geklickt haben. Dies bedeutet, dass ADF den Vorgang nicht ausführen konnte, weil von Ihrem Computer keine Verbindung mit dem ADF-Dienst hergestellt werden kann.

![Fehler bei der Verbindung](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

Um das Problem zu beheben, können Sie zunächst versuchen, den gleichen Vorgang in Ihrem Browser im InPrivate-Browsermodus auszuführen.

Wenn das dann immer noch nicht funktioniert, suchen Sie in der Fehlermeldung den **Servernamen** (in diesem Beispiel **dpnortheurope.svc.datafactory.azure.com**), und geben Sie dann den **Servernamen** direkt in die Adressleiste Ihres Browsers ein. 

- Wenn im Browser der Fehlercode 404 angezeigt wird, bedeutet dies in der Regel, dass Ihre Clientseite in Ordnung ist und das Problem auf der ADF-Dienstseite vorliegt. Übermitteln Sie ein Supportticket mit der **Aktivitäts-ID** aus der Fehlermeldung.

    ![Ressource nicht gefunden](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

- Wenn nicht der Fehlercode 404 angezeigt wird oder unten im Browser ein ähnlicher Fehler angezeigt wird, bedeutet dies in der Regel, dass ein clientseitiges Problem vorliegt. Befolgen Sie die weiteren Schritte zur Problembehandlung.

    ![Clientseitiger Fehler](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

Öffnen Sie zur weiteren Problembehandlung eine **Eingabeaufforderung**, und geben Sie `nslookup dpnortheurope.svc.datafactory.azure.com` ein. Das Ergebnis sieht normalerweise in etwa wie folgt aus:

![Befehlsantwort 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

- Wenn eine normale DNS-Antwort angezeigt wird, wenden Sie sich an den lokalen IT-Support, um die Firewalleinstellungen in Bezug darauf zu überprüfen, ob die HTTPS-Verbindung mit diesem Hostnamen blockiert ist. Wenn das Problem nicht behoben werden konnte, übermitteln Sie ein Supportticket mit der **Aktivitäts-ID** aus der Fehlermeldung.

- Wenn etwas anderes angezeigt wird, bedeutet dies in der Regel, dass auf dem DNS-Server beim Auflösen des DNS-Namens ein Fehler aufgetreten ist. Eine mögliche Problemumgehung besteht darin, den Internetdienstanbieter (Internet Service Provider, ISP) oder den DNS (z. B. in den Google-DNS 8.8.8.8) zu ändern. Wenn das Problem weiterhin besteht, könnten Sie versuchen, `nslookup datafactory.azure.com` und `nslookup azure.com` auszuführen, um zu ermitteln, auf welcher Ebene die DNS-Auflösung fehlgeschlagen ist, und dann alle Informationen an Ihren lokalen IT-Support oder Ihren ISP zur Problembehandlung übermitteln. Wenn der Support der Meinung ist, dass das Problem weiterhin aufseiten von Microsoft vorliegt, übermitteln Sie ein Supportticket mit der **Aktivitäts-ID** aus der Fehlermeldung.

    ![Befehlsantwort 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Ändern des verknüpften Diensttyps in Datasets

Datasets im Dateiformat können mit allen dateibasierten Connectors verwendet werden. Sie können z. B. ein Parquet-Dataset für Azure Blob Storage oder Azure Data Lake Storage Gen2 konfigurieren. Beachten Sie, dass die einzelnen Connectors unterschiedliche datenspeicherbezogene Einstellungen für die Aktivität sowie unterschiedliche App-Modelle unterstützen. 

Wenn Sie auf der ADF-Benutzeroberfläche für die Erstellung ein Dataset im Dateiformat für eine Aktivität (einschließlich „Kopieren“, „Suchen“, „Metadaten abrufen“ und „Löschen“) verwenden und im Dataset auf einen verknüpften Dienst verweisen möchten, dessen Typ nicht dem aktuellen Typ entspricht (z. B. ein Wechsel von Dateisystem zu ADLS Gen2), wird die folgende Warnmeldung angezeigt. Für einen reibungslosen Wechsel werden die Pipelines und Aktivitäten, die auf dieses Dataset verweisen, nach Ihrer Zustimmung so geändert, dass sie ebenfalls den neuen Typ verwenden. Da vorhandene Datenspeichereinstellungen, die nicht mit dem neuen Typ kompatibel sind, nicht mehr gültig sind, werden diese Einstellungen gelöscht.

Weitere Informationen zu den unterstützten Datenspeichereinstellungen für die verschiedenen Connectors finden Sie im entsprechenden Artikel zum jeweiligen Connector. Kopieren Sie die Aktivitätseigenschaften, um eine detaillierte Liste der Eigenschaften anzuzeigen. Weitere Informationen zu [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure Files](connector-azure-file-storage.md), [Dateisystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [Hadoop Distributed File System](connector-hdfs.md), [HTTP](connector-http.md) und [SFTP](connector-sftp.md).

![Warnmeldung](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="could-not-load-resource-while-opening-pipeline"></a>Ressource konnte beim Öffnen der Pipeline nicht geladen werden 

Wenn der Benutzer mithilfe des Erstellungstools der ADF-Benutzeroberfläche auf die Pipeline zugreift, wird die Fehlermeldung „Ressource ‚xxxxxx‘ konnte nicht geladen werden“ angezeigt. Stellen Sie sicher, dass die JSON-Datei keine Fehler enthält und dass die referenzierten Ressourcen vorhanden sind. Status: TypeError: Die Eigenschaft „xxxxx“ eines undefinierten Verweises kann nicht gelesen werden. Mögliche Ursache: TypeError: „Die Eigenschaft ‚xxxxxxx‘ eines undefinierten Verweises kann nicht gelesen werden.“

Die Quelle der Fehlermeldung ist die JSON-Datei, in der die Pipeline beschrieben wird. Dieser Fehler tritt auf, wenn der Kunde die Git-Integration verwendet und JSON-Pipelinedateien aus irgendeinem Grund beschädigt werden. Links neben dem Pipelinenamen wird ein Fehler (roter Punkt mit x) angezeigt, wie unten dargestellt.

![JSON-Pipelinefehler](media/data-factory-ux-troubleshoot-guide/pipeline-json-error.png)

Die Lösung besteht darin, zuerst die JSON-Dateien zu korrigieren und dann die Pipeline mithilfe des Erstellungstools erneut zu öffnen.



## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

* [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
* [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-Videos](https://azure.microsoft.com/resources/videos/index/)
* [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-data-factory.html)

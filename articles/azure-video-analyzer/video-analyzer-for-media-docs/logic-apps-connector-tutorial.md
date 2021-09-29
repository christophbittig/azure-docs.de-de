---
title: Die Tutorial für Connectors für Azure Video Analyzer for Media (zuvor Video Indexer) mit Logik-App und Power Automate.
description: In diesem Tutorial wird veranschaulicht, wie Sie neue Umgebungen und Monetarisierungschancen nutzen können, indem Sie Connectors für Azure Video Analyzer for Media (zuvor Video Indexer) mit Logic Apps und Power Automate verwenden.
ms.author: alzam
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 3650d82ec5d35b6b4e9826fd7a9d8579e40bb543
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626150"
---
# <a name="tutorial-use-video-analyzer-for-media-with-logic-app-and-power-automate"></a>Tutorial: Verwenden von Video Analyzer for Media mit Logic Apps und Power Automate

Die [REST-API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Video) für Azure Video Analyzer for Media (zuvor Video Indexer) von Azure Media Services unterstützt sowohl die Kommunikation von Server zu Server als auch von Client zu Server. Benutzer von Video Analyzer for Media können Erkenntnisse, die anhand von Video- und Audiodaten gewonnen werden, leicht in ihre Anwendungslogik integrieren und so neue Umgebungen und Monetarisierungschancen schaffen.

Zur weiteren Vereinfachung der Integration werden Connectors für [Logic Apps](https://azure.microsoft.com/services/logic-apps/) und [Power Automate](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/) unterstützt, die mit unserer API kompatibel sind. Mit den Connectors können Sie benutzerdefinierte Workflows einrichten, um auf effektive Weise für eine große Zahl von Video- und Audiodateien Erkenntnisse zu indizieren und zu extrahieren, ohne auch nur eine Codezeile zu schreiben. Wenn Sie die Connectors für Ihre Integration verwenden, haben Sie außerdem einen besseren Überblick über die Integrität Ihres Workflows und können ihn leicht debuggen.  

Damit Sie schnell mit der Verwendung der Video Analyzer for Media-Connectors beginnen können, ist hier eine exemplarische Vorgehensweise für eine Lösung mit Logic Apps und Power Automate beschrieben, die Sie einrichten können. In diesem Tutorial erfahren Sie, wie Sie Flows mithilfe von Logic Apps einrichten. Die Editoren und Funktionen in beiden Lösungen sind allerdings nahezu identisch, daher gelten die Diagramme und Erklärungen sowohl für Logic Apps als auch für Power Automate.

Das in diesem Tutorial beschriebene Szenario „Automatisches Hochladen und Indizieren Ihres Videos“ umfasst zwei verschiedene Flows, die zusammenarbeiten. 
* Der erste Workflow wird ausgelöst, wenn ein Blob in einem Azure Storage-Konto hinzugefügt oder geändert wird. Hierbei wird die neue Datei in Video Analyzer for Media mit einer Rückruf-URL hochgeladen, um eine Benachrichtigung senden zu können, nachdem der Indizierungsvorgang abgeschlossen ist. 
* Der zweite Workflow wird basierend auf der Rückruf-URL ausgelöst, und die extrahierten Erkenntnisse werden als JSON-Datei in Azure Storage gespeichert. Dieser Ansatz mit zwei Workflows wird verwendet, um das effektive asynchrone Hochladen und Indizieren von größeren Dateien zu unterstützen. 

In diesem Tutorial wird eine Logik-App verwendet, um Folgendes zu zeigen:

> [!div class="checklist"]
> * Einrichten des Workflows für den Dateiupload
> * Einrichten des Workflows für die JSON-Extraktion

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Zunächst benötigen Sie ein Video Analyzer for Media-Konto sowie [Zugriff auf die APIs per API-Schlüssel](video-indexer-use-apis.md). 
* Sie benötigen außerdem ein Azure Storage-Konto. Notieren Sie sich den Zugriffsschlüssel für Ihr Storage-Konto. Erstellen Sie zwei Container: einen zum Speichern von Videos und einen zum Speichern der von Video Analyzer for Media generierten Erkenntnisse.  
* Als Nächstes müssen Sie – entweder für Logic Apps oder Power Automate – zwei separate Workflows öffnen (je nachdem, was Sie verwenden). 

## <a name="set-up-the-first-flow---file-upload"></a>Einrichten des ersten Flows: Dateiupload   

Der erste Workflow wird jeweils ausgelöst, wenn in Ihrem Azure Storage-Container ein Blob hinzugefügt wird. Nach dem Auslösen wird ein SAS-URI erstellt, den Sie verwenden können, um das Video in Video Analyzer for Media hochzuladen und zu indizieren. In diesem Abschnitt wird der folgende Flow erstellt: 

![Workflow für Dateiupload](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Zum Einrichten des ersten Workflows müssen Sie Ihren Video Analyzer for Media-API-Schlüssel und die Azure Storage-Anmeldeinformationen angeben. 

![Azure Blob Storage](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Verbindungsname und API-Schlüssel](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

> [!TIP]
> Wenn Sie zuvor ein Azure Storage-Konto oder ein Video Analyzer for Media-Konto mit einer Logic App verbunden hatten, sind Ihre Verbindungsdetails gespeichert, und Sie werden automatisch verbunden. <br/>Sie können die Verbindung bearbeiten, indem Sie unten in einer Azure Storage-Aktion (im Speicherfenster) oder Video Analyzer for Media-Aktion (im Player-Fenster) auf **Verbindung ändern** klicken.

Wenn Sie eine Verbindung mit Ihrem Azure Storage- und Ihrem Video Analyzer for Media-Konto herstellen können, suchen Sie im **Designer für Logik-Apps** nach dem Trigger „Wenn ein Blob hinzugefügt oder geändert wird“, und wählen Sie ihn aus.

Wählen Sie den Container aus, in dem Sie Ihre Videodateien platzieren. 

![Screenshot: Dialogfeld „Wenn ein Blob hinzugefügt oder geändert wird“, in dem Sie einen Container auswählen können](./media/logic-apps-connector-tutorial/container.png)

Suchen Sie als Nächstes nach der Aktion „SAS-URI nach Pfad erstellen“, und wählen Sie sie aus. Wählen Sie im Dialogfeld für die Aktion in den Optionen für dynamischen Inhalt den Pfad zur Dateiliste aus.  

Fügen Sie außerdem einen neuen Parameter vom Typ „Protokoll für gemeinsamen Zugriff“ hinzu. Wählen Sie „HttpsOnly“ als Wert für den Parameter aus.

![SAS-URI nach Pfad](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Geben Sie den [Standort Ihres Kontos](regions.md) und die [Konto-ID](./video-indexer-use-apis.md#account-id) an, um das Token für das Video Analyzer for Media-Konto abzurufen.

![Abrufen des Tokens für den Kontozugriff](./media/logic-apps-connector-tutorial/account-access-token.png)

Geben Sie für „Upload video and index“ (Video hochladen und indizieren) die erforderlichen Parameter und die Video-URL an. Wählen Sie „Neuen Parameter hinzufügen“ und dann „Rückruf-URL“ aus. 

![Hochladen und Indizieren](./media/logic-apps-connector-tutorial/upload-and-index.png)

Lassen Sie die Rückruf-URL vorerst leer. Sie fügen sie erst nach Abschluss des zweiten Workflows hinzu, bei dem die Rückruf-URL erstellt wird. 

Sie können den Standardwert für die anderen Parameter verwenden oder den Wert gemäß Ihren Anforderungen festlegen. 

Klicken Sie auf **Speichern**. Wir fahren nun mit dem Konfigurieren des zweiten Workflows fort, bei dem die Erkenntnisse extrahiert werden, nachdem das Hochladen und Indizieren abgeschlossen ist. 

## <a name="set-up-the-second-flow---json-extraction"></a>Einrichten des zweiten Flows: JSON-Extraktion  

Nach Abschluss des Hochladens und Indizierens aus dem ersten Workflow wird eine HTTP-Anforderung mit der richtigen Rückruf-URL gesendet, um den zweiten Workflow auszulösen. Anschließend werden die von Video Analyzer for Media generierten Erkenntnisse abgerufen. In diesem Beispiel wird die Ausgabe Ihres Indizierungsauftrags auf Ihrer Azure Storage-Instanz gespeichert.  Es liegt aber an Ihnen, was Sie mit der Ausgabe machen.  

Erstellen Sie den zweiten Workflow separat vom ersten Workflow. 

![Workflow für JSON-Extraktion](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

Zum Einrichten des ersten Workflows müssen Sie erneut Ihren Video Analyzer for Media-API-Schlüssel und die Azure Storage-Anmeldeinformationen angeben. Sie müssen die gleichen Parameter wie beim ersten Workflow aktualisieren. 

Für Ihren Trigger wird ein Feld für die HTTP POST-URL angezeigt. Die URL wird erst generiert, nachdem Sie Ihren Workflow gespeichert haben. Sie benötigen die URL dann später aber noch. Wir kommen dann darauf zurück. 

Geben Sie den [Standort Ihres Kontos](regions.md) und die [Konto-ID](./video-indexer-use-apis.md#account-id) an, um das Token für das Video Analyzer for Media-Konto abzurufen.  

Navigieren Sie zur Aktion „Get Video Index“ (Videoindex abrufen), und geben Sie die erforderlichen Parameter an. Geben Sie als Video-ID den folgenden Ausdruck ein: triggerOutputs()['queries']['id'] 

![Info zur Video Analyzer for Media-Aktion](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Mit diesem Ausdruck wird der Connector angewiesen, die Video-ID aus der Ausgabe Ihres Triggers abzurufen. In diesem Fall ist die Ausgabe Ihres Triggers die Ausgabe von „Upload video and index“ (Video hochladen und indizieren) in Ihrem ersten Trigger. 

Navigieren Sie zur Aktion „Create blob“ (Blob erstellen), und wählen Sie den Pfad zum Ordner aus, in dem Sie die Erkenntnisse speichern möchten. Legen Sie den Namen des Blobs fest, das Sie erstellen. Geben Sie für den Blobinhalt den folgenden Ausdruck ein: body(‘Get_Video_Index’) 

![Aktion „Create blob“ (Blob erstellen)](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Bei diesem Ausdruck wird die Ausgabe der Aktion „Get Video Index“ (Videoindex abrufen) aus diesem Workflow verwendet. 

Klicken Sie auf **Flow speichern**. 

Nachdem der Workflow gespeichert wurde, wird im Trigger eine HTTP POST-URL erstellt. Kopieren Sie die URL aus dem Trigger. 

![Speichern des URL-Triggers](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Kehren Sie zum ersten Flow zurück, und fügen Sie die URL aus der Aktion „Upload video and index“ (Video hochladen und indizieren) als Parameter für die Rückruf-URL ein. 

Stellen Sie sicher, dass beide Workflows gespeichert wurden und alles bereit ist! 

Probieren Sie Ihre neu erstellte Lösung mit Logic Apps bzw. Power Automate aus, indem Sie Ihrem Azure-Blobcontainer ein Video hinzufügen und nach einigen Minuten Wartezeit überprüfen, ob die Erkenntnisse im Zielordner angezeigt werden. 

## <a name="generate-captions"></a>Erstellen von Untertiteln

Im folgenden Blog finden Sie die Schritte, die zeigen, wie Sie [Untertitel mit Video Analyzer for Media und Logic Apps erstellen](https://techcommunity.microsoft.com/t5/azure-media-services/generating-captions-with-video-indexer-and-logic-apps/ba-p/1672198). 

Der Artikel zeigt außerdem, wie ein Video durch Kopieren auf OneDrive automatisch indiziert wird und wie die von Video Analyzer for Media generierten Untertitel auf OneDrive gespeichert werden.
 
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie dieses Tutorial durchgearbeitet haben, können Sie die Lösung mit Logic Apps bzw. Power Automate bei Bedarf weiter ausführen. Falls Sie die weitere Ausführung nicht wünschen und das Anfallen von Gebühren vermeiden möchten, müssen Sie bei Verwendung von Power Automate Ihre beiden Workflows deaktivieren. Deaktivieren Sie beide Workflows, falls Sie Logic Apps verwenden. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde nur ein Beispiel für Video Analyzer for Media-Connectors veranschaulicht. Sie können die Connectors von Video Analyzer for Media für jeden von Video Analyzer for Media bereitgestellten API-Aufruf verwenden. Beispiel: Hochladen und Abrufen von Erkenntnissen, Übersetzen der Ergebnisse, Nutzen von einbettungsfähigen Widgets und sogar Anpassen Ihrer Modelle. Darüber hinaus können Sie sich dafür entscheiden, dass diese Aktionen basierend auf unterschiedlichen Quellen ausgelöst werden, z. B. Updates von Dateirepositorys oder gesendete E-Mails. Sie können dann auswählen, dass die Ergebnisse gemäß unserer relevanten Infrastruktur oder Anwendung aktualisiert werden sollen oder eine beliebige Anzahl von Aktionselementen generiert werden soll.  

> [!div class="nextstepaction"]
> [Verwenden der Video Analyzer for Media-API](video-indexer-use-apis.md)

Weitere Ressourcen finden Sie unter [Video Analyzer for Media](/connectors/videoindexer-v2/).

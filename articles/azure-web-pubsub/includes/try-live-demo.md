---
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/08/2021
ms.openlocfilehash: 1cd52fc7930407317d426b3e2615202dce37e860
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751170"
---
## <a name="get-the-client-url-with-a-temp-access-token"></a>Abrufen der Client-URL mit einem temporären Zugriffstoken

Im Azure-Portal steht ein einfacher Client-URL-Generator zum Generieren einer temporären URL für schnelle Tests/Überprüfungen zur Verfügung. Verwenden Sie dieses Tool, um eine temporäre Clientzugriffs-URL zu erhalten und eine Verbindung mit der Instanz herzustellen.

- Navigieren Sie zum Azure-Portal, und ermitteln Sie die Azure Web PubSub-Instanz.
- Navigieren Sie auf dem Blatt `Key` zu `Client URL Generator`. 
- Legen Sie die entsprechenden Rollen (`Roles`) fest: **An Gruppen senden** und **Gruppen beitreten/verlassen**.
- Generieren und kopieren Sie `Client Access URL`. 

:::image type="content" source="../media/quickstart-live-demo/generate-client-url.png" alt-text="Screenshot des Generierens der Client-URL":::

## <a name="try-the-instance-with-an-online-demo"></a>Testen der Instanz mit einer Onlinedemo

Mit dieser Livedemo können Sie problemlos einer Gruppe beitreten oder diese verlassen und Nachrichten an die Gruppenmitglieder senden:

> [!div class="nextstepaction"]
> [Öffnen der Demo](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)

> [!NOTE]
>  Die **Clientzugriffs-URL** im Portal dient der Benutzerfreundlichkeit, um Ihnen den Einstieg zu vereinfachen. Sie können diese Clientzugriffs-URL auch für einige schnelle Verbindungstests verwenden. Wenn Sie eine eigene Anwendung schreiben möchten, stehen Ihnen dafür SDKs in vier Sprachen zur Verfügung, mit deren Hilfe Sie die URL generieren können. 

- Probieren Sie unterschiedliche Gruppen zum Beitreten sowie verschiedene Gruppen aus, an die Nachrichten gesendet werden sollen, und sehen Sie sich an, welche Nachrichten empfangen werden. Zum Beispiel:
    - Lassen Sie zwei Clients derselben Gruppe beitreten. Sie werden sehen, dass die Nachricht an die Gruppenmitglieder übertragen werden kann. 
    - Lassen Sie zwei Clients unterschiedlichen Gruppen beitreten. Sie werden sehen, dass der Client keine Nachricht empfangen kann, wenn er kein Gruppenmitglied ist. 
- Sie können beim Generieren der `Client Access URL` auch `Roles` deaktivieren, um zu sehen, was geschieht, wenn Sie einer Gruppe beitreten oder Nachrichten an eine Gruppe senden. Zum Beispiel:
    - Deaktivieren Sie die Berechtigung `Send to Groups`. Sie werden sehen, dass der Client keine Nachrichten an die Gruppe senden kann. 
    - Deaktivieren Sie die Berechtigung `Join/Leave Groups`. Sie werden sehen, dass der Client keiner Gruppe beitreten kann. 

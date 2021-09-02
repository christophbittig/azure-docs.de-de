---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: ceb52a4e3e2b66388b73bcb63b68913d8dcb467a
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830515"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Abrufen der Azure Storage-Verbindungszeichenfolge

Zuvor haben Sie ein Azure Storage-Konto erstellt, das von der Funktions-App verwendet werden kann. Die Verbindungszeichenfolge für dieses Konto wird sicher in App-Einstellungen in Azure gespeichert. Indem Sie die Einstellung in die Datei *local.settings.json* herunterladen, können Sie diese Verbindung zum Schreiben in eine Storage-Warteschlange unter demselben Konto verwenden, wenn Sie die Funktion lokal ausführen. 

1. Führen Sie im Stammverzeichnis des Projekts den folgenden Befehl aus, indem Sie `<APP_NAME>` durch den Namen Ihrer Funktions-App aus der vorherigen Schnellstartanleitung ersetzen. Mit diesem Befehl werden alle vorhandenen Werte in der Datei überschrieben.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. Öffnen Sie *local.settings.json*, und suchen Sie nach dem Wert mit dem Namen `AzureWebJobsStorage`. Dies ist die Verbindungszeichenfolge des Storage-Kontos. Sie verwenden den Namen `AzureWebJobsStorage` und die Verbindungszeichenfolge noch in anderen Abschnitten dieses Artikels.

> [!IMPORTANT]
> Da *local.settings.json* aus Azure heruntergeladene Geheimnisse enthält, sollten Sie diese Datei immer aus der Quellcodeverwaltung ausschließen. In der *GITIGNORE*-Datei, die für ein lokales Funktionsprojekt erstellt wird, ist die Datei standardmäßig ausgeschlossen.
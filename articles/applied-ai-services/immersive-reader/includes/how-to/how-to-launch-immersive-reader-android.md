---
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: nitinme
ms.openlocfilehash: e080a0b7cc05b776e3085715e20d21bfb2545dd7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253718"
---
## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Eine Ressource des plastischen Readers, die für die Authentifizierung mit Azure Active Directory konfiguriert ist. Befolgen Sie [diese Anweisungen](../../how-to-create-immersive-reader.md) für die Einrichtung.  Einige der hier erstellten Werte benötigen Sie bei der Konfiguration der Umgebungseigenschaften. Speichern Sie die Ausgabe Ihrer Sitzung zur späteren Verwendung in einer Textdatei.
* [Git](https://git-scm.com/).
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Android Studio](https://developer.android.com/studio)

## <a name="configure-authentication-credentials"></a>Konfigurieren von Anmeldeinformationen für die Authentifizierung

1. Starten Sie Android Studio, und öffnen Sie das Projekt im Verzeichnis **immersive-reader-sdk/js/samples/quickstart-java-android** (Java) oder im Verzeichnis **immersive-reader-sdk/js/samples/quickstart-kotlin** (Kotlin).

1. Erstellen Sie eine Datei mit dem Namen **env** im Ordner **/assets**. Fügen Sie die folgenden Namen und Werte hinzu, und geben Sie entsprechende Werte an. Committen Sie diese Datei nicht in die Quellcodeverwaltung, da sie Geheimnisse enthält, die nicht für die Öffentlichkeit bestimmt sind.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>Starten von Plastischer Reader mit Beispielinhalt

Wählen Sie im AVD-Manager einen Geräteemulator aus, und führen Sie das Projekt aus.

## <a name="next-steps"></a>Nächste Schritte

* Machen Sie sich mit dem [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) und der [zugehörigen Referenz](../../reference.md) vertraut.
* Sehen Sie sich Codebeispiele auf [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) an.
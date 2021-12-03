---
title: 'Schnellstart: Erstellen einer Dienstverbindung in App Service im Azure-Portal'
description: In diesem Schnellstart erfahren Sie, wie Sie eine Dienstverbindung in App Service im Azure-Portal herstellen.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fcab4ce8d1f0d4d6f2dd7d29b1de8f9c7519c9f1
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851874"
---
# <a name="quickstart-create-a-service-connection-in-app-service-from-azure-portal"></a>Schnellstart: Erstellen einer Dienstverbindung in App Service im Azure-Portal

In diesem Schnellstart erfahren Sie, wie Sie eine neue Dienstverbindung mit dem Dienstconnector in App Service im Azure-Portal erstellen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

In diesem Schnellstart wird davon ausgegangen, dass Sie bereits über mindestens einen in Azure ausgeführten App Service verfügen. [Erstellen Sie einen App Service](../app-service/quickstart-dotnetcore.md), wenn Sie über keinen App Service verfügen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com/](https://portal.azure.com/) mit Ihrem Azure-Konto beim Azure-Portal an.

## <a name="create-a-new-service-connection-in-app-service"></a>Erstellen einer neuen Dienstverbindung in App Service

Sie verwenden den Dienstconnector, um eine neue Dienstverbindung in App Service zu erstellen.

1. Wählen Sie im linken Bereich des Azure-Portals die Schaltfläche **Alle Ressourcen** aus. Geben Sie **App Service** im Filter ein, und klicken Sie in der Liste auf den Namen des App Services, den Sie verwenden möchten.
2. Wählen Sie im linken Inhaltsverzeichnis **Dienstconnector (Vorschau)** aus. Klicken Sie anschließend auf **Erstellen**.
3. Wählen Sie die folgenden Einstellungen aus, oder geben Sie sie ein.

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonnement** | Eines Ihrer Abonnements | Das Abonnement, in dem sich Ihr Zieldienst befindet (der Dienst, mit dem Sie eine Verbindung herstellen möchten). Der Standardwert ist das Abonnement, in dem sich dieser App Service befindet. |
    | **Diensttyp** | Blob Storage | Zieldiensttyp. Wenn Sie über keinen Storage Blobcontainer verfügen, können Sie [einen erstellen](../storage/blobs/storage-quickstart-blobs-portal.md) oder einen anderen Diensttyp verwenden. |
    | **Verbindungsname** | Generierter eindeutiger Name | Der Verbindungsname, der die Verbindung zwischen Ihrem App Service und dem Zieldienst identifiziert.  |
    | **Speicherkonto** | Ihr Speicherkonto | Das Zielspeicherkonto, mit dem Sie eine Verbindung herstellen möchten. Wenn Sie einen anderen Diensttyp auswählen, müssen Sie die entsprechende Zieldienstinstanz auswählen. |
    | **Clienttyp** | Der gleiche App-Stapel wie für diesen App Service | Ihr Anwendungsstapel, der mit dem ausgewählten Zieldienst zusammenarbeitet. Der Standardwert stammt aus dem App Service-Laufzeitstapel. |

4. Wählen Sie **Weiter: Authentifizierung** aus, um den Authentifizierungstyp auszuwählen. Wählen Sie dann **Verbindungszeichenfolge** aus, um den Zugriffsschlüssel zum Herstellen einer Verbindung mit Ihrem Blob-Speicherkonto zu verwenden.

5. Wählen Sie dann **Weiter: Überprüfen und erstellen** aus, um die bereitgestellten Informationen zu überprüfen. Wählen Sie dann **Erstellen** aus, um die Dienstverbindung zu erstellen. Es kann eine Minute dauern, bis der Vorgang abgeschlossen ist.

## <a name="view-service-connections-in-app-service"></a>Anzeigen von Dienstverbindungen in App Service

1. In **Dienstconnector (Vorschau)** wird eine App Service-Verbindung mit dem Zieldienst angezeigt.

1. Klicken Sie auf die Schaltfläche **>** , um die Liste zu erweitern. Dadurch werden die Umgebungsvariablen angezeigt, die für Ihren Anwendungscode erforderlich sind.

1. Klicken Sie auf die Schaltfläche **...** , und wählen Sie **Überprüfen** aus. Daraufhin werden die Details zur Verbindungsüberprüfung im Popupblatt von rechts angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie die unten stehenden Tutorials durch, um mit dem Erstellen Ihrer eigenen Anwendung mit dem Dienstconnector zu beginnen.

> [!div class="nextstepaction"]
> [Tutorial: WebApp + Storage mit Azure CLI](./tutorial-csharp-webapp-storage-cli.md)

> [!div class="nextstepaction"]
> [Tutorial: WebApp + PostgreSQL mit Azure CLI](./tutorial-django-webapp-postgres-cli.md)

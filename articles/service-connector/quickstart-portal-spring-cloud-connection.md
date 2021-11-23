---
title: 'Schnellstart: Erstellen einer Dienstverbindung in Spring Cloud im Azure-Portal'
description: In diesem Schnellstart erfahren Sie, wie Sie eine Dienstverbindung in Spring Cloud im Azure-Portal erstellen.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 001e014e616f3ee199fd59d11c385732b0bed568
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301284"
---
# <a name="quickstart-create-a-service-connection-in-spring-cloud-from-azure-portal"></a>Schnellstart: Erstellen einer Dienstverbindung in Spring Cloud im Azure-Portal

In diesem Schnellstart erfahren Sie, wie Sie eine neue Dienstverbindung mit dem Dienstconnector in Spring Cloud im Azure-Portal erstellen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

In diesem Schnellstart wird davon ausgegangen, dass Sie bereits über mindestens eine in Azure ausgeführte Spring Cloud-Anwendung verfügen. Wenn Sie nicht über eine Spring Cloud-Anwendung verfügen, [erstellen Sie eine](../spring-cloud/quickstart.md).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com/](https://portal.azure.com/) mit Ihrem Azure-Konto beim Azure-Portal an.

## <a name="create-a-new-service-connection-in-spring-cloud"></a>Erstellen einer neuen Dienstverbindung in Spring Cloud

Sie verwenden den Dienstconnector, um eine neue Dienstverbindung in Spring Cloud zu erstellen.

1. Wählen Sie im linken Bereich des Azure-Portals die Schaltfläche **Alle Ressourcen** aus. Geben Sie **Spring Cloud** im Filter ein, und klicken Sie in der Liste auf den Namen der Spring Cloud, die Sie verwenden möchten.
1. Wählen Sie **Apps** und dann den Anwendungsnamen in der Liste aus.
1. Wählen Sie im linken Inhaltsverzeichnis **Dienstconnector (Vorschau)** aus. Klicken Sie anschließend auf **Erstellen**.
1. Wählen Sie die folgenden Einstellungen aus, oder geben Sie sie ein.

    | Einstellung      | Vorgeschlagener Wert  | Beschreibung                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonnement** | Eines Ihrer Abonnements | Das Abonnement, in dem sich Ihr Zieldienst befindet (der Dienst, mit dem Sie eine Verbindung herstellen möchten). Der Standardwert ist das Abonnement, in dem sich dieser App Service befindet. |
    | **Diensttyp** | Blob Storage | Zieldiensttyp. Wenn Sie über keinen Blobspeicher verfügen, können Sie [einen erstellen](../storage/blobs/storage-quickstart-blobs-portal.md) oder einen anderen Diensttyp verwenden. |
    | **Verbindungsname** | Generierter eindeutiger Name | Der Verbindungsname, der die Verbindung zwischen Ihrem App Service und dem Zieldienst identifiziert.  |
    | **Speicherkonto** | Ihr Speicherkonto | Das Zielspeicherkonto, mit dem Sie eine Verbindung herstellen möchten. Wenn Sie einen anderen Diensttyp auswählen, müssen Sie die entsprechende Zieldienstinstanz auswählen. |

1. Wählen Sie **Weiter: Authentifizierung** aus, um den Authentifizierungstyp auszuwählen. Wählen Sie dann **Verbindungszeichenfolge** aus, um den Zugriffsschlüssel zum Herstellen einer Verbindung mit Ihrem Blob-Speicherkonto zu verwenden.
1. Wählen Sie dann **Weiter: Überprüfen und erstellen** aus, um die bereitgestellten Informationen zu überprüfen. Wählen Sie dann **Erstellen** aus, um die Dienstverbindung zu erstellen. Es kann eine Minute dauern, bis der Vorgang abgeschlossen ist.

## <a name="view-service-connections-in-spring-cloud"></a>Anzeigen von Dienstverbindungen in Spring Cloud

1. In **Dienstconnector (Vorschau)** wird eine Spring Cloud-Verbindung mit dem Zieldienst angezeigt.

1. Klicken Sie auf die Schaltfläche **>** , um die Liste zu erweitern. Dadurch werden die Eigenschaften angezeigt, die für Ihre Spring Boot-Anwendung erforderlich sind.

1. Klicken Sie auf die Schaltfläche **...** , und wählen Sie **Überprüfen** aus. Daraufhin werden die Details zur Verbindungsüberprüfung im Popupblatt von rechts angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie die unten stehenden Tutorials durch, um mit dem Erstellen Ihrer eigenen Anwendung mit dem Dienstconnector zu beginnen.

> [!div class="nextstepaction"]
> [Tutorial: Spring Cloud und MySQL](./tutorial-java-spring-mysql.md)

> [!div class="nextstepaction"]
> [Tutorial: Spring Cloud und Apache Kafka in Confluent Cloud](./tutorial-java-spring-confluent-kafka.md)

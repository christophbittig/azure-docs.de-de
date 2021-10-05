---
title: Datei einfügen
description: include file
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 09/07/2021
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 9d33df05e54d9cdeb97631ca479dd8ac9869343c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "126056671"
---
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im Menü auf der linken Seite die Option **Alle Dienste** und dann im Abschnitt **Mobil** die Option **Notification Hubs** aus. Wählen Sie das Sternsymbol neben dem Dienstnamen aus, um den Dienst im linken Menü zum Abschnitt **FAVORITEN** hinzuzufügen. Nachdem **Notification Hubs** unter **FAVORITEN** hinzugefügt wurde, können Sie diesen Eintrag im Menü auf der linken Seite auswählen.

      ![Azure-Portal – Auswählen von Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Wählen Sie auf der Seite **Notification Hubs** in der Symbolleiste die Option **Erstellen** aus.

      ![Notification Hubs – Symbolleistenschaltfläche „Hinzufügen“](./media/notification-hubs-portal-create-new-hub/create-toolbar-button.png)

1. Führen Sie auf der Registerkarte **Grundlagen** der Seite **Notification Hub** die folgenden Schritte aus:

    1. Wählen Sie unter **Abonnement** den Namen des Azure-Abonnements aus, das Sie verwenden möchten, und wählen Sie dann eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe.  

    1. Geben Sie unter **Namespacedetails** einen eindeutigen Namen für den neuen Namespace ein. 
    
    1. Ein Namespace enthält mindestens einen Notification Hub. Geben Sie daher unter **Notification Hub-Details** einen Namen für den Hub ein. Alternativ können Sie in der Dropdownliste einen vorhandenen Namespace auswählen.

    1. Wählen Sie im Dropdown-Listenfeld **Standort** einen Wert aus. Dieser Wert gibt den Standort an, an dem der Hub erstellt werden soll.

    1. Klicken Sie auf **Erstellen**.

        ![Azure-Portal – Festlegen von Eigenschaften für den Notification Hub](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Wählen Sie **Benachrichtigungen** (Glockensymbol) und dann **Zu Ressource wechseln** aus. Sie können auch die Liste auf der Seite **Notification Hubs** aktualisieren und Ihren Hub auswählen.

      ![Azure-Portal: zu Ressource navigieren](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Wählen Sie in der Liste die Option **Zugriffsrichtlinien** aus. Notieren Sie sich die beiden Verbindungszeichenfolgen, die für Sie verfügbar sind. Sie werden später für die Behandlung von Pushbenachrichtigungen benötigt.

      >[!IMPORTANT]
      >Verwenden Sie *nicht* die Richtlinie **DefaultFullSharedAccessSignature** in Ihrer Anwendung. Diese ist nur für die Verwendung in Ihrem Back-End vorgesehen.
      >

      ![Azure-Portal – Verbindungszeichenfolgen für den Notification Hub](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

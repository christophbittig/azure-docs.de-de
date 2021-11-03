---
title: Erstellen einer Azure Notification Hub-Instanz über das Azure-Portal | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie über das Azure-Portal eine Azure Notification Hub-Instanz erstellen.
services: notification-hubs
author: sethmanheim
ms.author: sethm
manager: femila
ms.reviewer: thsomasu
ms.date: 08/04/2020
ms.lastreviewed: 02/14/2019
ms.topic: quickstart
ms.service: notification-hubs
ms.workload: mobile
ms.custom: mode-portal
ms.openlocfilehash: 1f9ba3f32a2b7f5b0845593ec3439cf826b50ce6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070126"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Schnellstart: Erstellen einer Azure Notification Hub-Instanz über das Azure-Portal

Azure Notification Hubs bietet ein benutzerfreundliches, horizontal skalierbares Pushmodul, mit dem Sie von einem beliebigen Back-End (cloudbasiert oder lokal) Benachrichtigungen an sämtliche Plattformen (iOS, Android, Windows, Kindle, Baidu usw.) senden können. Weitere Informationen zu diesem Dienst finden Sie unter [Was ist Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

In dieser Schnellstartanleitung erstellen Sie einen Notification Hub über das Azure-Portal. Im ersten Abschnitt erfahren Sie, wie Sie einen Notification Hubs-Namespace und einen Hub in diesem Namespace erstellen. Im zweiten Abschnitt erfahren Sie, wie Sie einen Notification Hub in einem bereits vorhandenen Notification Hubs-Namespace erstellen.

## <a name="create-a-namespace-and-a-notification-hub"></a>Erstellen eines Namespace und eines Notification Hubs

In diesem Abschnitt erstellen Sie einen Namespace und einem Hub in dem Namespace.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Erstellen eines Notification Hubs in einem vorhandenen Namespace

In diesem Abschnitt erstellen Sie einen Notification Hub in einem vorhandenen Namespace.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im linken Menü die Option **Alle Dienste** aus, suchen Sie nach **Notification Hub**, und wählen Sie neben **Notification Hub-Namespaces** den **Stern** (`*`) aus, um die Option dem Abschnitt **FAVORITEN** des linken Menüs hinzuzufügen. Wählen Sie **Notification Hub-Namespaces** aus.

      ![Azure-Portal: Auswählen von „Notification Hub-Namespaces“](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Wählen Sie in der Liste auf der Seite **Notification Hub-Namespaces** Ihren Namespace aus.

      ![Auswählen Ihres Namespace in der Liste](./media/create-notification-hub-portal/select-namespace.png)
4. Wählen Sie auf der Symbolleiste der Seite **Notification Hub-Namespaces** die Option **Hub hinzufügen** aus.

      ![Notification Hub-Namespaces: Schaltfläche „Hub hinzufügen“](./media/create-notification-hub-portal/add-hub-button.png)
5. Geben Sie auf der Seite **Neuer Notification Hub** einen Namen für den Notification Hub ein, und wählen Sie **OK** aus.

      ![Seite „Neuer Notification Hub“: Eingeben eines Hub-Namens](./media/create-notification-hub-portal/new-notification-hub-page.png)
6. Wählen Sie oben **Benachrichtigungen** (Glockensymbol) aus, um den Status der Bereitstellung des neuen Hubs anzuzeigen. Wählen Sie in der rechten oberen Ecke **X** aus, um das Benachrichtigungsfenster zu schließen.

      ![Bereitstellungsbenachrichtigung](./media/create-notification-hub-portal/deployment-notification.png)
7. Aktualisieren Sie die Webseite **Notification Hub-Namespaces**, damit Ihr neuer Hub in der Liste angezeigt wird.

      ![Screenshot: Webseite „Notification Hub-Namespaces“ mit dem neuen Hub in der Liste](./media/create-notification-hub-portal/new-hub-in-list.png)
8. Wählen Sie Ihren **Notification Hub** aus, um dessen Startseite anzuzeigen.

      ![Screenshot: Startseite für den Notification Hub](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Notification Hub erstellt. Unter [Konfigurieren von Azure Notification Hubs mit Einstellungen des Plattformbenachrichtigungssystems über das Azure-Portal](configure-notification-hub-portal-pns-settings.md) erfahren Sie, wie Sie den Hub mit Einstellungen des Plattformbenachrichtigungssystems (Platform Notification System, PNS) konfigurieren.

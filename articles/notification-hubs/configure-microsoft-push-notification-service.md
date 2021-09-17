---
title: Konfigurieren von Microsoft Push Notification Service in Azure Notification Hubs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Microsoft Push Notification Service-Einstellungen für einen Azure Notification Hub konfigurieren.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/23/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: c2b46d3f4831ed98e54a47d8daa77107a625e362
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770896"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Konfigurieren der Einstellungen des Microsoft-Pushbenachrichtigungsdiensts (MPNS) im Azure-Portal

> [!NOTE]
> Der Microsoft-Pushbenachrichtigungsdienst (MPNS) ist veraltet und wird nicht mehr unterstützt.

In diesem Artikel wird gezeigt, wie Sie Microsoft Push Notification Service-Einstellungen (MPNS) für einen Azure Notification Hub über das Azure-Portal konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie noch keinen Notification Hub erstellt haben, erstellen Sie ihn jetzt. Weitere Informationen finden Sie unter [Erstellen einer Azure Notification Hub-Instanz über das Azure-Portal](create-notification-hub-portal.md).

## <a name="configure-microsoft-push-notification-service-mpns"></a>Konfigurieren des Microsoft Push Notification Service (MPNS)

Die folgende Vorgehensweise beschreibt, wie Sie die Microsoft Push Notification Service-Einstellungen (MPNS) für einen Notification Hub konfigurieren:

1. Wählen Sie im Azure-Portal auf der Seite **Notification Hub** im Menü auf der linken Seite die Option **Windows Phone (MPNS)** aus.
2. Aktivieren Sie entweder nicht authentifizierte oder authentifizierte Pushbenachrichtigungen:

   a. Um nicht authentifizierte Pushbenachrichtigungen zu aktivieren, wählen Sie **Nicht authentifizierte Pushbenachrichtigungen zulassen** > **Speichern** aus.

      ![Screenshot, der das Aktivieren nicht authentifizierter Pushbenachrichtigungen zeigt](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. So aktivieren Sie authentifizierte Pushbenachrichtigungen:
      * Wählen Sie auf der Symbolleiste die Option **Zertifikat hochladen** aus.
      * Wählen Sie das Dateisymbol und anschließend die Zertifikatdatei aus.
      * Geben Sie das Kennwort für das Zertifikat ein.
      * Klicken Sie auf **OK**.
      * Wählen Sie auf der Seite **Windows Phone (MPNS)** die Option **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

Ein Tutorial mit Schrittanleitungen zum Pushen von Benachrichtigungen an Windows Phone-Geräte mithilfe von Azure Notification Hubs und Microsoft Push Notification Service (MPNS) finden Sie unter [Senden von Pushbenachrichtigung an Windows Phone-Apps mithilfe von Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

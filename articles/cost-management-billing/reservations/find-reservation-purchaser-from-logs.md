---
title: Ermitteln eines Reservierungskäufers über Azure Monitor-Protokolle
description: In diesem Artikel wird erläutert, wie Sie mit Informationen aus Azure Monitor-Protokollen einen Reservierungskäufer ermitteln.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: baf7fe1968f2ba2a72e27f1caf85d02c65a3e93f
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514754"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Ermitteln eines Reservierungskäufers über Azure-Protokolle

In diesem Artikel wird erläutert, wie Sie mit Informationen aus den Verzeichnisprotokollen einen Reservierungskäufer ermitteln. In den Verzeichnisprotokollen von Azure Monitor werden die E-Mail-IDs von Benutzern angezeigt, die Reservierungskäufe getätigt haben.

## <a name="find-the-purchaser"></a>Ermitteln des Käufers

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Monitor** > **Aktivitätsprotokoll** > **Aktivität**.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Screenshot: Navigation zu „Aktivitätsprotokoll“ > „Aktivität“" lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Wählen Sie **Verzeichnisaktivität** aus. Wenn die Meldung *You need permission to view directory-level logs* (Sie benötigen Berechtigungen zum Anzeigen von Protokollen auf Verzeichnisebene.) angezeigt wird, wählen Sie [diesen Link](../../role-based-access-control/elevate-access-global-admin.md) aus, um zu erfahren, wie Sie Berechtigungen erhalten.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Screenshot: Verzeichnisaktivität ohne Berechtigung zum Anzeigen des Protokolls" lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. Wenn Sie über die Berechtigung verfügen, filtern Sie **Mandantenressourcenanbieter** nach **Microsoft.Capacity**. Es sollten alle reservierungsbezogenen Ereignisse für den ausgewählten Zeitraum angezeigt werden. Ändern Sie bei Bedarf den Zeitraum.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Screenshot: Benutzer, der die Reservierung erworben hat" lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    Sie müssen ggf. die Option **Spalten bearbeiten** verwenden, um **Ereignis initiiert von** auswählen zu können.
   Der Benutzer, der die Reservierung erworben hat, wird unter **Ereignis initiiert von** angezeigt.

## <a name="next-steps"></a>Nächste Schritte

- Bei Bedarf können Abrechnungsadministratoren [den Besitz einer Reservierung übernehmen](view-reservations.md#view-and-manage-reservations).

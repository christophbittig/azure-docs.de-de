---
title: Einrichten der technischen Konfiguration eines Angebots für Dynamics 365 for Customer Engagement und PowerApps in Microsoft AppSource – Azure Marketplace
description: Richten Sie die technische Konfiguration eines Angebots für Dynamics 365 for Customer Engagement und PowerApps in Microsoft AppSource (Azure Marketplace) ein.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 06/29/2021
ms.openlocfilehash: 6d58fbdd2f6b4b89d2584b9f82e0f0fd0417ad35
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113085108"
---
# <a name="set-up-dynamics-365-for-customer-engagement--power-apps-offer-technical-configuration"></a>Einrichten der technischen Konfiguration eines Angebots für Dynamics 365 for Customer Engagement und PowerApps

Auf dieser Seite werden die technischen Informationen definiert, die für das Herstellen einer Verbindung mit Ihrem Angebot verwendet werden. Über diese Verbindung können wir Ihr Angebot den Kunden bereitstellen, die es erwerben möchten.

## <a name="offer-information"></a>Angebotsinformationen

Das **Basislizenzmodell** bestimmt, wie Kunden im CRM Admin Center Ihrer Anwendung zugewiesen werden. Führen Sie einen der folgenden Schritte aus:

- Wählen Sie **Ressource** für instanzbasierte Lizenzierung aus.
- Wählen Sie **Benutzer** aus, wenn Lizenzen jeweils pro Mandant zugewiesen werden oder Ihre App-Lizenzen über Microsoft verwaltet werden sollen.

Das Kontrollkästchen **Erfordert eine ausgehende S2S-Verbindung und CRM-Secure Store-Zugriff** ermöglicht die Konfiguration von ausgehendem CRM Secure Store- oder Server-zu-Server-Zugriff (S2S). Dieses Feature muss während der Zertifizierungsphase besonders vom Dynamics 365-Team berücksichtigt werden. Microsoft kontaktiert Sie, um zusätzliche Schritte ausführen, die dieses Feature unterstützen.

Lassen Sie **Anwendungskonfigurations-URL leer**. Dies ist für die zukünftige Verwendung vorgesehen.

## <a name="crm-package"></a>CRM-Paket

Geben Sie im Feld **URL Ihres Paketspeicherorts** die URL des Azure Blob Storage-Kontos ein, das die hochgeladene ZIP-Datei des CRM-Pakets enthält. Schließen Sie einen schreibgeschützten SAS-Schlüssel in die URL ein, damit Microsoft Ihr Paket zur Überprüfung öffnen kann.

> [!IMPORTANT]
> Um eine Veröffentlichungssperre zu vermeiden, stellen Sie sicher, dass das Ablaufdatum in der URL Ihres Blobspeichers nicht abgelaufen ist. Sie können das Datum überarbeiten, indem Sie auf Ihre Richtlinie zugreifen. Es wird empfohlen, den **Ablaufzeitpunkt** mindestens einen Monat in der Zukunft anzusetzen.

Aktivieren Sie ggf. das Feld **In der Paketdatei sind mehrere CRM-Pakete enthalten**. Wenn dies der Fall ist, stellen Sie sicher, dass Sie alle Pakete in die ZIP-Datei einschließen.

Ausführliche Informationen zum Erstellen des Pakets und zum Aktualisieren seiner Struktur finden Sie in [Schritt 3: Erstellen eines AppSource-Pakets für Ihre App](/powerapps/developer/common-data-service/create-package-app-appsource).

## <a name="crm-package-availability"></a>Verfügbarkeit von CRM-Paketen

Wählen Sie **+ Region hinzufügen** aus, um die geografischen Regionen anzugeben, in denen Ihr CRM-Paket für Kunden verfügbar sein wird. Wählen Sie keine der folgenden unabhängigen Regionen (Sovereign Regions) aus: US Gov Cloud (Sovereign), China Cloud (Sovereign), Deutschland (Sovereign), US Gov High Cloud (Sovereign), Test in Production (Sovereign) oder US DoD Cloud (Sovereign).

Standardmäßig wird die zuvor eingegebene **Anwendungskonfigurations-URL** für jede Region verwendet. Lassen Sie das Feld „Anwendungskonfigurations-URL“ leer.

Wählen Sie **Entwurf speichern**, und fahren Sie dann mit der nächsten Registerkarte im linken Navigationsmenü fort, **Co-Sell mit Microsoft**. Informationen zum Einrichten von Co-Selling mit Microsoft (optional) finden Sie unter [Co-Selling mit Microsoft-Vertriebsteams und -Partnern: Übersicht](./co-sell-overview.md). Wenn Sie kein Co-Sell einrichten oder fertig sind, fahren Sie mit **Weitere Schritte** unten fort.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren zusätzlicher Inhalte](dynamics-365-customer-engage-supplemental-content.md)
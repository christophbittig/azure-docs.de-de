---
title: Konfigurieren der aktiven Georeplikation für Azure Cache for Redis-Enterprise-Instanzen
description: Erfahren Sie, wie Ihre Azure Cache for Redis-Enterprise-Instanzen über Azure-Regionen hinweg repliziert werden.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 5f7bd55cfbdd0e38e2c53784dd24760fb051c0f1
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112233581"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Konfigurieren der aktiven Georeplikation für Azure Cache for Redis-Enterprise-Instanzen (Vorschau)

In diesem Artikel erfahren Sie, wie Sie einen aktiven georeplizierten Azure Cache über das Azure-Portal konfigurieren.

Die aktive Georeplikation gruppiert zwei Azure Cache for Redis-Enterprise-Instanzen in einem einzelnen Cache, der mehrere Azure-Regionen umfasst. Beide Instanzen fungieren als lokale primäre Replikate. Eine Anwendung entscheidet, welche Instanzen für Lese- und Schreibanforderungen verwendet werden sollen.

> [!NOTE]
> Die Datenübertragung zwischen Azure-Regionen wird mit den [Standardbandbreitensätzen](https://azure.microsoft.com/pricing/details/bandwidth/) abgerechnet.

## <a name="create-or-join-an-active-geo-replication-group"></a>Erstellen von oder Beitreten zu einer aktiven Georeplikationsgruppe

> [!IMPORTANT]
> Die aktive Georeplikation muss aktiviert werden, wenn ein Azure Cache for Redis erstellt wird.
>
>

1. Wählen Sie auf der Registerkarte **Erweitert** der Erstellungsbenutzeroberfläche **Neuer Redis Cache** die Option **Enterprise** für **Clusterrichtlinie** aus.

    ![Konfigurieren der aktiven Georeplikation](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. Wählen Sie **Konfigurieren** aus, um die **aktive Georeplikation** einzurichten.

1. Erstellen Sie eine neue Replikationsgruppe für eine erste Cache-Instanz, oder wählen Sie eine vorhandene aus der Liste aus.

    ![Verknüpfen von Caches](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. Wählen Sie **Konfigurieren** aus, um den Vorgang abzuschließen.

    ![Konfigurierte aktive Georeplikation](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. Warten Sie, bis der erste Cache erfolgreich erstellt wurde. Wiederholen Sie die obigen Schritte für jede zusätzliche Cache-Instanz in der Georeplikationsgruppe.

## <a name="remove-from-an-active-geo-replication-group"></a>Entfernen aus einer aktiven Georeplikationsgruppe

Um eine Cache-Instanz aus einer aktiven Georeplikationsgruppe zu entfernen, löschen Sie einfach die Instanz. Die verbleibenden Instanzen konfigurieren sich automatisch selbst neu.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Azure Cache for Redis-Features.

* [Azure Cache for Redis-Dienstebenen](cache-overview.md#service-tiers)
* [Hochverfügbarkeit für Azure Cache for Redis](cache-high-availability.md)

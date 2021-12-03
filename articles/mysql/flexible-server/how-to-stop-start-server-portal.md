---
title: Anhalten/Starten – Azure-Portal – Azure Database for MySQL Flexible Server
description: In diesem Artikel wird beschrieben, wie Sie Vorgänge in Azure Database for MySQL im Azure-Portal anhalten/starten.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: e17b788c3852d104d82b456e03c93420f3ea9f3d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467837"
---
# <a name="stopstart-an-azure-database-for-mysql---flexible-server"></a>Anhalten/Starten einer Azure Database für MySQL-flexible Server

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Dieser Artikel enthält Schritt-für-Schritt-Anleitungen zum Anhalten und Starten der Flexible Server-Instanz.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

-   Sie müssen über Azure Database for MySQL Flexible Server verfügen.

## <a name="stop-a-running-server"></a>Beenden eines ausgeführten Servers

1.  Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Flexible Server-Instanz aus, die Sie beenden möchten.

2.  Klicken Sie auf der Seite **Übersicht** in der Symbolleiste auf die Schaltfläche **Beenden**.

    :::image type="content" source="media/how-to-stop-start-server-portal/stop-server.png" alt-text="Beenden Sie die Flexible Server-Instanz.":::

3.  Klicken Sie auf **Ja**, um das Beenden Ihrer Server-Instanz zu bestätigen.

    :::image type="content" source="media/how-to-stop-start-server-portal/confirm-stop.png" alt-text="Bestätigen Sie das Beenden der Flexible Server-Instanz.":::

> [!NOTE]
> Sobald der Server beendet wurde, sind keine anderen Verwaltungsvorgänge für die Flexible Server-Instanz mehr verfügbar.

## <a name="start-a-stopped-server"></a>Starten eines beendeten Servers

1.  Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Flexible Server-Instanz aus, die Sie starten möchten.

2.  Klicken Sie auf der Seite **Übersicht** in der Symbolleiste auf die Schaltfläche **Starten**.

    :::image type="content" source="media/how-to-stop-start-server-portal/start-server.png" alt-text="Starten Sie die Flexible Server-Instanz.":::

> [!NOTE]
> Sobald der Server gestartet wurde, sind alle anderen Verwaltungsvorgänge für die Flexible Server-Instanz wieder verfügbar.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Netzwerke in Azure Database for MySQL Flexible Server](./concepts-networking.md).
- [Erstellen und Verwalten von virtuellen Netzwerken für Azure Database for MySQL Flexible Server mit dem Azure-Portal](./how-to-manage-virtual-network-portal.md).


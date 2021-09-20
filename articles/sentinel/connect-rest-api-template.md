---
title: Verbinden Ihrer Datenquelle mit der Datensammler-API von Azure Sentinel zur Erfassung von Daten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie externe Systeme mit der Datensammler-API von Azure Sentinel verbinden, um deren Protokolldaten in benutzerdefinierten Protokollen in Ihrem Arbeitsbereich zu erfassen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2021
ms.author: yelevin
ms.openlocfilehash: cda5595eb2c8b72f6b38ade1feefe84904312412
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123261135"
---
# <a name="connect-your-data-source-to-azure-sentinels-data-collector-api-to-ingest-data"></a>Verbinden Ihrer Datenquelle mit der Datensammler-API von Azure Sentinel zur Erfassung von Daten

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

API-Integrationen von Drittanbietern rufen Daten per Pull aus den Datenquellen ihrer Produkte ab und stellen eine Verbindung mit der [Azure Monitor-Datensammler-API](../azure-monitor/logs/data-collector-api.md) von Azure Sentinel her, um die Daten per Push in benutzerdefinierte Protokolltabellen in Ihrem Azure Sentinel-Arbeitsbereich zu übertragen.

In den meisten Fällen finden Sie alle für die Konfiguration dieser Datenquellen erforderlichen Informationen zum Herstellen einer Verbindung mit Azure Sentinel in der Dokumentation des jeweiligen Anbieters.

Überprüfen Sie auf der Referenzseite für [Azure Sentinel-Datenconnectors](data-connectors-reference.md) im Abschnitt zu Ihrem Produkt, ob weitere Anweisungen vorhanden sind. Zudem finden Sie dort Links zu den Anweisungen Ihres Anbieters.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich.

- Sie müssen über Leseberechtigungen für freigegebene Schlüssel für den Arbeitsbereich verfügen. [Weitere Informationen zu Arbeitsbereichsschlüsseln](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)

## <a name="configure-and-connect-your-data-source"></a>Konfigurieren und Verbinden Ihrer Datenquelle

1. Klicken Sie im Navigationsmenü des Azure Sentinel-Portals auf **Datenconnectors**.

1. Wählen Sie im Katalog der Datenconnectors Ihr Produkt aus, und klicken Sie anschließend auf die Schaltfläche **Connectorseite öffnen**.

1. Führen Sie die Schritte aus, die auf der Connectorseite angezeigt werden, oder folgen Sie ggf. den angezeigten Links zu den Anweisungen des Anbieters.

1. Wenn Sie zur Eingabe der Arbeitsbereichs-ID und des Primärschlüssels aufgefordert werden, kopieren Sie diese auf der Datenconnectorseite, und fügen Sie sie gemäß den Anweisungen Ihres Anbieters in die Konfiguration ein. Betrachten Sie das folgende Beispiel.

    :::image type="content" source="media/connect-rest-api-template/workspace-id-primary-key.png" alt-text="Arbeitsbereichs-ID und Primärschlüssel":::

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine Verbindung erfolgreich hergestellt wurde, werden die Daten im Abschnitt **CustomLogs** unter **Protokolle** angezeigt. Die Tabellennamen finden Sie auf der Referenzseite für [Azure Sentinel-Datenconnectors](data-connectors-reference.md) im Abschnitt zu Ihrem Produkt.

Verwenden Sie zur Abfrage der Daten aus Ihrem Produkt diese Tabellennamen.

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie externe Datenquellen mit der Datensammler-API von Azure Sentinel verbinden. Wenn Sie die Vorteile der integrierten Funktionen dieser Datenconnectors in vollem Umfang nutzen möchten, klicken Sie auf der Datenconnectorseite auf die Registerkarte **Nächste Schritte**. Dort finden Sie einige vorgefertigte Beispielabfragen, Arbeitsmappen und Vorlagen für Analyseregeln, die Ihnen die Suche nach nützlichen Informationen erleichtern.

Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.

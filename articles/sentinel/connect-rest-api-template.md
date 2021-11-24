---
title: Verbinden Sie Ihre Datenquelle mit der Microsoft Sentinel Data Collector API, um Daten zu übernehmen | Microsoft Docs
description: Erfahren Sie, wie Sie externe Systeme mit der Microsoft Sentinel Data Collector API verbinden, um deren Protokolldaten in benutzerdefinierte Protokolle in Ihrem Arbeitsbereich einzuspeisen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 439bdac80e5fef514a7de7e9617be42738c6c420
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524529"
---
# <a name="connect-your-data-source-to-the-microsoft-sentinel-data-collector-api-to-ingest-data"></a>Verbinden Sie Ihre Datenquelle mit der Microsoft Sentinel Data Collector API, um Daten aufzunehmen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

API-Integrationen von Drittanbietern ziehen Daten aus den Datenquellen ihrer Produkte und stellen eine Verbindung zur Microsoft Sentinel [Azure Monitor Data Collector API](../azure-monitor/logs/data-collector-api.md) her, um die Daten in benutzerdefinierte Protokolltabellen in Ihrem Microsoft Sentinel-Arbeitsbereich zu übertragen.

In den meisten Fällen finden Sie in der Dokumentation des jeweiligen Anbieters alle Informationen, die Sie benötigen, um diese Datenquellen für die Verbindung mit Microsoft Sentinel zu konfigurieren.

Überprüfen Sie auf der Referenzseite für [Azure Sentinel-Datenconnectors](data-connectors-reference.md) im Abschnitt zu Ihrem Produkt, ob weitere Anweisungen vorhanden sind. Zudem finden Sie dort Links zu den Anweisungen Ihres Anbieters.

> [!NOTE]
> Die Daten werden an dem geografischen Ort gespeichert, an dem sich der Arbeitsbereich befindet, auf dem Sie Microsoft Sentinel ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen über Lese- und Schreibberechtigungen für den Microsoft Sentinel-Arbeitsbereich verfügen.

- Sie müssen über Leseberechtigungen für freigegebene Schlüssel für den Arbeitsbereich verfügen. [Weitere Informationen zu Arbeitsbereichsschlüsseln](../azure-monitor/agents/agent-windows.md)

## <a name="configure-and-connect-your-data-source"></a>Konfigurieren und Verbinden Ihrer Datenquelle

1. Wählen Sie im Microsoft Sentinel-Portal im Navigationsmenü die Option **Datenkonnektoren**.

1. Wählen Sie im Katalog der Datenconnectors Ihr Produkt aus, und klicken Sie anschließend auf die Schaltfläche **Connectorseite öffnen**.

1. Führen Sie die Schritte aus, die auf der Connectorseite angezeigt werden, oder folgen Sie ggf. den angezeigten Links zu den Anweisungen des Anbieters.

1. Wenn Sie zur Eingabe der Arbeitsbereichs-ID und des Primärschlüssels aufgefordert werden, kopieren Sie diese auf der Datenconnectorseite, und fügen Sie sie gemäß den Anweisungen Ihres Anbieters in die Konfiguration ein. Betrachten Sie das folgende Beispiel.

    :::image type="content" source="media/connect-rest-api-template/workspace-id-primary-key.png" alt-text="Arbeitsbereichs-ID und Primärschlüssel":::

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine Verbindung erfolgreich hergestellt wurde, werden die Daten im Abschnitt **CustomLogs** unter **Protokolle** angezeigt. Die Tabellennamen finden Sie auf der Referenzseite für [Azure Sentinel-Datenconnectors](data-connectors-reference.md) im Abschnitt zu Ihrem Produkt.

Verwenden Sie zur Abfrage der Daten aus Ihrem Produkt diese Tabellennamen.

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie man externe Datenquellen mit der Microsoft Sentinel Data Collector API verbindet. Wenn Sie die Vorteile der integrierten Funktionen dieser Datenconnectors in vollem Umfang nutzen möchten, klicken Sie auf der Datenconnectorseite auf die Registerkarte **Nächste Schritte**. Dort finden Sie einige vorgefertigte Beispielabfragen, Arbeitsmappen und Vorlagen für Analyseregeln, die Ihnen die Suche nach nützlichen Informationen erleichtern.

Weitere Informationen über Microsoft Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Erste Schritte [Erkennung von Bedrohungen mit Microsoft Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.

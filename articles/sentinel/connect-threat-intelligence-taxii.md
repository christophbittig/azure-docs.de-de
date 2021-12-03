---
title: Herstellen einer Verbindung zwischen Microsoft Azure Sentinel und STIX-/TAXII-Threat-Intelligence-Feeds | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Microsoft Azure Sentinel mit branchenüblichen Threat-Intelligence-Feeds verbinden, um Bedrohungsindikatoren zu importieren.
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
ms.openlocfilehash: 570d296b7f6f8a2831d4f758be5b85108da5d8a2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521774"
---
# <a name="connect-microsoft-sentinel-to-stixtaxii-threat-intelligence-feeds"></a>Herstellen einer Verbindung zwischen Microsoft Azure Sentinel und STIX-/TAXII-Threat-Intelligence-Feeds

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Siehe auch**: [Verbinden Ihrer Threat-Intelligence-Plattform (TIP) mit Microsoft Azure Sentinel](connect-threat-intelligence-tip.md)

Der am weitesten verbreitete Branchenstandard für die Übertragung von Threat Intelligence-Daten ist eine [Kombination aus dem STIX-Datenformat und dem TAXII-Protokoll](https://oasis-open.github.io/cti-documentation/). Wenn Ihre Organisation die Bedrohungsindikatoren aus Lösungen empfängt, die die aktuelle STIX-/TAXII-Version (2.0 oder 2.1) unterstützen, können Sie Ihre Bedrohungsindikatoren mit dem **TAXII-Datenconnector für Threat Intelligence** in Microsoft Azure Sentinel importieren. Dieser Connector ermöglicht es einem integrierten TAXII-Client in Microsoft Azure Sentinel, Threat Intelligence von TAXII 2.x-Servern zu importieren.

:::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-taxii-import-path.png" alt-text="TAXII-Importpfad":::

Erfahren Sie mehr über [Threat Intelligence](understand-threat-intelligence.md) in Microsoft Azure Sentinel und insbesondere über die [TAXII-Threat-Intelligence-Feeds ](threat-intelligence-integration.md#taxii-threat-intelligence-feeds), die in Microsoft Azure Sentinel integriert werden können.

## <a name="prerequisites"></a>Voraussetzungen  

- Sie benötigen Lese- und Schreibberechtigungen für den Microsoft Azure Sentinel-Arbeitsbereich, um Ihre Bedrohungsindikatoren zu speichern.
- Sie müssen über einen **API-Stamm-URI** und eine **Sammlungs-ID** für TAXII 2.0 oder TAXII 2.1 verfügen.

## <a name="instructions"></a>Anweisungen

Führen Sie die folgenden Schritte aus, um STIX-formatierte Bedrohungsindikatoren von einem TAXII-Server in Microsoft Azure Sentinel zu importieren:

1. Abrufen des API-Stamms und der Sammlungs-ID des TAXII-Servers

1. Aktivieren des TAXII-Datenconnectors für Threat Intelligence in Microsoft Azure Sentinel

### <a name="get-the-taxii-server-api-root-and-collection-id"></a>Abrufen des API-Stamms und der Sammlungs-ID des TAXII-Servers

TAXII 2.x-Server kündigen API-Stammadressen an, bei denen es sich um URLs handelt, unter denen Threat Intelligence-Sammlungen gehostet werden. Den API-Stamm und die Sammlungs-ID finden Sie normalerweise auf den Dokumentationsseiten des Threat Intelligence-Anbieters, der den TAXII-Server hostet. 

> [!NOTE]
> Mitunter gibt der Anbieter nur eine URL bekannt, die als Ermittlungsendpunkt bezeichnet wird. Mit dem Hilfsprogramm cURL können Sie den Ermittlungsendpunkt abfragen und den API-Stamm anfordern, [wie im Folgenden beschrieben](#find-the-api-root).

### <a name="enable-the-threat-intelligence---taxii-data-connector-in-microsoft-sentinel"></a>Aktivieren des TAXII-Datenconnectors für Threat Intelligence in Microsoft Azure Sentinel

Führen Sie die folgenden Schritte aus, um Bedrohungsindikatoren von einem TAXII-Server in Microsoft Azure Sentinel zu importieren:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu **Microsoft Azure Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren vom TAXII-Server importieren möchten.

1. Wählen Sie im Menü **Datenconnectors** im Connectorkatalog **Threat Intelligence – TAXII** aus, und wählen Sie die Schaltfläche **Connectorseite öffnen** aus.

1. Geben Sie einen **Anzeigenamen** für diese TAXII-Serversammlung, die **API-Stamm-URL**, die **Sammlungs-ID**, einen **Benutzernamen** (falls erforderlich) und ein **Kennwort** (falls erforderlich) ein, und wählen Sie die Gruppe der Indikatoren und die gewünschte Abfragefrequenz. Wählen Sie die Schaltfläche **Hinzufügen** aus.

    :::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-configure-taxii-servers.png" alt-text="Konfigurieren von TAXII-Servern":::
 
Sie sollten eine Bestätigung erhalten, dass eine Verbindung mit dem TAXII-Server hergestellt wurde. Sie können den letzten Schritt so oft wie gewünscht wiederholen, um eine Verbindung mit mehreren Sammlungen auf einem oder mehreren TAXII-Servern herzustellen.

Innerhalb weniger Minuten sollten Bedrohungsindikatoren in diesen Microsoft Azure Sentinel-Arbeitsbereich fließen. Sie finden die neuen Indikatoren auf dem Blatt **Threat Intelligence**, das Sie über das Hauptmenü von Microsoft Azure Sentinel öffnen können.

### <a name="find-the-api-root"></a>Ermitteln des API-Stamms

Es folgt ein Beispiel für die Verwendung des Befehlszeilenprogramms [cURL](https://en.wikipedia.org/wiki/CURL), das in Windows und den meisten Linux-Distributionen enthalten ist, um den API-Stamm zu ermitteln und die Sammlungen eines TAXII-Servers zu durchsuchen, wobei nur der Ermittlungsendpunkt angegeben ist. Über den Ermittlungsendpunkt des ThreatStream TAXII 2.0-Servers [Anomali Limo](https://www.anomali.com/community/limo) können Sie den API-Stamm-URI und anschließend die Sammlungen anfordern.

1.  Navigieren Sie in einem Browser zum Ermittlungsendpunkt des ThreatStream TAXII 2.0-Servers unter https://limo.anomali.com/taxii , um den API-Stamm abzurufen. Authentifizieren Sie sich mit dem Benutzernamen und dem Kennwort `guest`.

    Sie empfangen die folgende Antwort:

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  Verwenden Sie das Dienstprogramm cURL und den API-Stamm (https://limo.anomali.com/api/v1/taxii2/feeds/) ) aus der vorherigen Antwort, an den Sie `collections/` anfügen, um die Liste der im API-Stamm gehosteten Sammlungs-IDs zu durchsuchen:

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    ```
    Nachdem Sie sich erneut mit dem Kennwort „guest“ authentifiziert haben, erhalten Sie die folgende Antwort:

    ```json
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Sie verfügen nun über alle Informationen, die Sie benötigen, um Microsoft Azure Sentinel mit mindestens einer TAXII-Serversammlung zu verbinden, die von Anomali Limo bereitgestellt werden.

| **API-Stamm** (https://limo.anomali.com/api/v1/taxii2/feeds/) | Sammlungs-ID |
| ------------------------------------------------------------ | ------------: |
| **Phish Tank**                                               | 107           |
| **Abuse.ch – Ransomware-IP-Adressen**                                  | 135           |
| **Abuse.ch – Ransomware-Domänen**                              | 136           |
| **DShield – Scan-IP-Adressen**                                     | 150           |
| **Malware Domain List – Hotlist**                            | 200           |
| **Blutmagie – TOR-Knoten**                                      | 209           |
| **Emerging Threats – C&C-Server**                              |  31           |
| **Lehigh – Schadsoftwaredomänen**                                    |  33           |
| **CyberCrime**                                               |  41           |
| **Emerging Threats – kompromittiert**                           |  68           |
|

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Microsoft Azure Sentinel mithilfe des TAXII-Protokolls mit Threat-Intelligence-Feeds verbinden können. Weitere Informationen zu Microsoft Azure Sentinel finden Sie in den folgenden Artikeln.

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit [Erkennung von Bedrohungen mithilfe von Microsoft Azure Sentinel](./detect-threats-built-in.md).

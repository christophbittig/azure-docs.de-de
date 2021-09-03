---
title: Verbinden von Azure Sentinel mit STIX/TAXII-Threat Intelligence-Feeds | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Sentinel mit branchenüblichen Threat Intelligence-Feeds verbinden, um Bedrohungsindikatoren zu importieren.
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
ms.date: 07/12/2021
ms.author: yelevin
ms.openlocfilehash: ce1d2fafd330848c19dc5386fad11d87bfe2670a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339332"
---
# <a name="connect-azure-sentinel-to-stixtaxii-threat-intelligence-feeds"></a>Verbinden von Azure Sentinel mit STIX/TAXII-Threat Intelligence-Feeds

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Siehe auch**: [Verbinden Ihrer Threat Intelligence-Plattform (TP) mit Azure Sentinel](connect-threat-intelligence-tip.md)

Der am weitesten verbreitete Branchenstandard für die Übertragung von Threat Intelligence-Daten ist eine [Kombination aus dem STIX-Datenformat und dem TAXII-Protokoll](https://oasis-open.github.io/cti-documentation/). Wenn Ihre Organisation die Bedrohungsindikatoren aus Lösungen empfängt, die die aktuelle STIX/TAXII-Version (2.0 oder 2.1) unterstützen, können Sie Ihre Bedrohungsindikatoren mit dem Datenconnector **Threat Intelligence – TAXII** in Azure Sentinel importieren. Dieser Datenconnector ermöglicht einem integrierten TAXII-Client in Azure Sentinel das Importieren von Threat Intelligence von TAXII 2.x-Servern.

:::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-taxii-import-path.png" alt-text="TAXII-Importpfad":::

Erfahren Sie mehr über [Threat Intelligence](understand-threat-intelligence.md) in Azure Sentinel und insbesondere über die [TAXII-Threat Intelligence-Feeds ](threat-intelligence-integration.md#taxii-threat-intelligence-feeds), die in Azure Sentinel integriert werden können.

## <a name="prerequisites"></a>Voraussetzungen  

- Sie benötigen Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich, um Ihre Bedrohungsindikatoren zu speichern.
- Sie müssen über einen **API-Stamm-URI** und eine **Sammlungs-ID** für TAXII 2.0 oder TAXII 2.1 verfügen.

## <a name="instructions"></a>Anweisungen

Gehen Sie folgendermaßen vor, um von einem TAXII-Server Bedrohungsindikatoren im STIX-Format in Azure Sentinel zu importieren:

1. Abrufen des API-Stamms und der Sammlungs-ID des TAXII-Servers

1. Aktivieren des Datenconnectors „Threat Intelligence – TAXII“ in Azure Sentinel

### <a name="get-the-taxii-server-api-root-and-collection-id"></a>Abrufen des API-Stamms und der Sammlungs-ID des TAXII-Servers

TAXII 2.x-Server kündigen API-Stammadressen an, bei denen es sich um URLs handelt, unter denen Threat Intelligence-Sammlungen gehostet werden. Den API-Stamm und die Sammlungs-ID finden Sie normalerweise auf den Dokumentationsseiten des Threat Intelligence-Anbieters, der den TAXII-Server hostet. 

> [!NOTE]
> Mitunter gibt der Anbieter nur eine URL bekannt, die als Ermittlungsendpunkt bezeichnet wird. Mit dem Hilfsprogramm cURL können Sie den Ermittlungsendpunkt abfragen und den API-Stamm anfordern, [wie im Folgenden beschrieben](#find-the-api-root).

### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Aktivieren des Datenconnectors „Threat Intelligence – TAXII“ in Azure Sentinel

Gehen Sie folgendermaßen vor, um von einem TAXII-Server Bedrohungsindikatoren in Azure Sentinel zu importieren:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zum Dienst **Azure Sentinel**.

1. Wählen Sie den **Arbeitsbereich** aus, in den Sie Bedrohungsindikatoren vom TAXII-Server importieren möchten.

1. Wählen Sie im Menü **Datenconnectors** im Connectorkatalog **Threat Intelligence – TAXII** aus, und klicken Sie auf die Schaltfläche **Connectorseite öffnen**.

1. Geben Sie einen **Anzeigenamen** für diese TAXII-Serversammlung, die **API-Stamm-URL**, die **Sammlungs-ID**, einen **Benutzernamen** (falls erforderlich) und ein **Kennwort** (falls erforderlich) ein, und wählen Sie die Gruppe der Indikatoren und die gewünschte Abfragefrequenz. Klicken Sie auf die Schaltfläche **Hinzufügen** .

    :::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-configure-taxii-servers.png" alt-text="Konfigurieren von TAXII-Servern":::
 
Sie sollten eine Bestätigung erhalten, dass eine Verbindung mit dem TAXII-Server hergestellt wurde. Sie können den letzten Schritt so oft wie gewünscht wiederholen, um eine Verbindung mit mehreren Sammlungen auf einem oder mehreren TAXII-Servern herzustellen.

Nach einigen Minuten sollten die Bedrohungsindikatoren in den Azure Sentinel-Arbeitsbereich fließen. Sie finden die neuen Indikatoren auf dem Blatt **Threat Intelligence**, auf das Sie über das Hauptmenü von Azure Sentinel Zugriff haben.

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

Sie verfügen nun über alle Informationen, die Sie zum Herstellen einer Verbindung von Azure Sentinel mit einer oder mehreren von Anomali Limo bereitgestellten TAXII-Serversammlungen benötigen.

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

In diesem Dokument haben Sie erfahren, wie Sie Azure Sentinel über das TAXII-Protokoll mit Threat Intelligence-Feeds verbinden können. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln.

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./detect-threats-built-in.md).

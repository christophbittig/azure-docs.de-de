---
title: Systemanforderungen für Microsoft Azure Data Box Heavy | Microsoft-Dokumentation
description: Erfahren Sie etwas über die Software- und Netzwerkanforderungen für Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 10/07/2021
ms.author: alkohli
ms.openlocfilehash: 692623de1d1e13dbdd5d09feb3197ededb8be9ee
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000069"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Systemanforderungen für Azure Data Box Heavy

In diesem Artikel werden wichtige Systemanforderungen für Ihre Microsoft Azure Data Box Heavy und die Clients beschrieben, die sich mit dem Gerät verbinden. Sie sollten die Informationen sorgfältig lesen, bevor Sie Ihre Data Box Heavy bereitstellen. Später können Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

Systemanforderungen:

* **Softwareanforderungen für Hosts, die sich mit der Data Box Heavy verbinden**: Beschreibt die unterstützten Plattformen, Browser für die lokale Webbenutzeroberfläche, SMB-Clients und alle zusätzlichen Anforderungen an Hosts, die sich mit der Data Box verbinden können.
* **Netzwerkanforderungen für Data Box Heavy**: Enthält Informationen zu den Netzwerkanforderungen für einen optimalen Betrieb der Data Box Heavy.

## <a name="software-requirements"></a>Softwareanforderungen

Die Softwareanforderungen umfassen Informationen zu den unterstützten Betriebssystemen, unterstützten Browsern für die lokale Webbenutzeroberfläche und SMB-Clients.

### <a name="supported-operating-systems-for-clients"></a>Unterstützte Betriebssysteme für Clients

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Unterstützte Dateisysteme für Linux-Clients

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Unterstützte Speicherkonten

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Unterstützte Speichertypen

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Unterstützte Webbrowser

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Netzwerkanforderungen

Ihr Datencenter verfügt über ein Hochgeschwindigkeitsnetzwerk. Zur Erzielung der höchstmöglichen Kopiergeschwindigkeit können zwei 40-GbE-Verbindungen (jeweils eine pro Knoten) parallel genutzt werden. Falls Sie über keine 40-GbE-Verbindung verfügen, sollten Sie mindestens zwei Verbindungen mit 10 GbE verwenden (jeweils eine pro Knoten).

### <a name="port-requirements"></a>Portanforderungen

In der folgenden Tabelle sind die Ports aufgeführt, die in der Firewall für SMB- oder NFS-Datenverkehr geöffnet werden müssen. In dieser Tabelle bezieht sich *ein* oder *eingehend* auf die Richtung, aus der eingehende Clientanforderungen auf das Gerät zugreifen. Entsprechend bezieht sich *aus* oder *ausgehend* auf die Richtung, in der das Data Box Heavy-Gerät Daten über die Bereitstellung hinaus an externe Ziele sendet: z. B. ausgehende Verbindungen mit dem Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen Ihrer Azure Data Box](data-box-deploy-ordered.md)

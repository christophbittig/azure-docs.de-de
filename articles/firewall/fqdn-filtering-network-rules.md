---
title: FQDN-Filterung von Azure Firewall in Netzwerkregeln
description: Verwenden der FQDN-Filterung von Azure Firewall in Netzwerkregeln
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/01/2021
ms.author: victorh
ms.openlocfilehash: 1aaea272e83bf39f2f4aa7e373b26ae28aadbda0
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430322"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>Verwenden der FQDN-Filterung in Netzwerkregeln

Ein vollqualifizierter Domänenname (Fully Qualified Domain Name, FQDN) stellt den Domänennamen eines Hosts oder von IP-Adressen dar. Sie können FQDNs in Netzwerkregeln verwenden, basierend auf der DNS-Auflösung in Azure Firewall und der Firewallrichtlinie. Diese Funktion ermöglicht es Ihnen, ausgehenden Datenverkehr mit einem beliebigen TCP/UDP-Protokoll (einschließlich NTP, SSH, RDP und mehr) zu filtern. Für die Verwendung von FQDNs in Ihren Netzwerkregeln müssen Sie den DNS-Proxy aktivieren. Weitere Informationen finden Sie unter [DNS-Einstellungen für Azure Firewall](dns-settings.md).

> [!NOTE]
> Die FQDN-Filterung in Netzwerkregeln unterstützt keine Wildcards.

## <a name="how-it-works"></a>Funktionsweise

Nachdem Sie definiert haben, welchen DNS-Server Ihre Organisation benötigt (Azure DNS oder Ihr eigenes benutzerdefiniertes DNS), übersetzt Azure Firewall den FQDN in IP-Adressen, basierend auf dem ausgewählten DNS-Server. Diese Übersetzung erfolgt bei der Verarbeitung von Anwendungs- und Netzwerkregeln.

Wenn eine neue DNS-Auflösung stattfindet, werden die neuen IP-Adressen zu Firewallregeln hinzugefügt. Alte IP-Adressen, die vom DNS-Server nicht mehr zurückgegeben werden, laufen nach 15 Minuten ab. Azure Firewall-Regeln werden ab der DNS-Auflösung der FQDNs in Netzwerkregeln alle 15 Sekunden aktualisiert.

### <a name="differences-in-application-rules-vs-network-rules"></a>Unterschiede zwischen Anwendungsregeln und Netzwerkregeln

- Die FQDN-Filterung in Anwendungsregeln für HTTP/S und MSSQL basiert auf einem auf Anwendungsebene transparenten Proxy und dem SNI-Header. Daher kann hierbei zwischen zwei FQDNs unterschieden werden, die zur selben IP-Adresse aufgelöst werden. Bei der Verwendung von FQDN-Filterung in Netzwerkregeln ist dies nicht der Fall. 

   Verwenden Sie nach Möglichkeit immer Anwendungsregeln:
  - Wenn das Protokoll HTTP/S oder MSSQL lautet, verwenden Sie Anwendungsregeln für die FQDN-Filterung.
  - Verwenden Sie für Dienste wie AzureBackup, HDInsight usw. Anwendungsregeln mit FQDN-Tags.
  - Bei allen anderen Protokollen können Sie Anwendungs- oder Netzwerkregeln für die FQDN-Filterung verwenden.

## <a name="next-steps"></a>Nächste Schritte

[DNS-Einstellungen für Azure Firewall](dns-settings.md)

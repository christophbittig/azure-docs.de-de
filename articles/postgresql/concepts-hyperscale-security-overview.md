---
title: Überblick über die Sicherheit – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Informationsschutz und Netzwerksicherheit für Azure Database for PostgreSQL – Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: dd3d359143185eb4d33ddce6396b7c69288fe841
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084329"
---
# <a name="security-in-azure-database-for-postgresql--hyperscale-citus"></a>Sicherheit in Azure Database for PostgreSQL – Hyperscale (Citus)

Auf dieser Seite werden die verschiedenen Sicherheitsebenen beschrieben, die zum Schutz der Daten in Ihrer Hyperscale(Citus)-Servergruppe verfügbar sind. 

## <a name="information-protection-and-encryption"></a>Informationsschutz und -verschlüsselung

### <a name="in-transit"></a>Während der Übertragung

Wenn Daten in einem Knoten erfasst werden, schützt Hyperscale (Citus) Ihre Daten, indem sie während der Übertragung mit Transport Layer Security 1.2 verschlüsselt werden. Verschlüsselung (SSL/TLS) wird immer erzwungen und kann nicht deaktiviert werden.

### <a name="at-rest"></a>Im Ruhezustand

Der flexible Hyperscale(Citus)-Dienst nutzt das FIPS 140-2-zertifizierte Kryptografiemodul für die Speicherverschlüsselung ruhender Daten. Daten, einschließlich Sicherungen, werden auf dem Datenträger verschlüsselt (einschließlich der temporären Dateien, die während der Ausführung von Abfragen erstellt wurden).
Der Dienst verwendet das in der Azure Storage-Verschlüsselung enthaltene AES-256-Bit-Verschlüsselungsverfahren, und die Schlüssel werden vom System verwaltet. Die Speicherverschlüsselung ist immer aktiviert und kann nicht deaktiviert werden.

## <a name="network-security"></a>Netzwerksicherheit

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

## <a name="limits-and-limitations"></a>Grenzwerte und Einschränkungen

Weitere Informationen finden Sie unter Hyperscale (Citus) – [Grenzwerte und Einschränkungen](concepts-hyperscale-limits.md).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [privaten Zugriff aktivieren und verwalten](howto-hyperscale-private-access.md) (Vorschau)
* Weitere Informationen zu [privaten Endpunkten](../private-link/private-endpoint-overview.md)
* Informieren Sie sich ausführlicher über [virtuelle Netzwerke](../virtual-network/concepts-and-best-practices.md).
* Erfahren Sie mehr über [private DNS-Zonen](../dns/private-dns-overview.md).
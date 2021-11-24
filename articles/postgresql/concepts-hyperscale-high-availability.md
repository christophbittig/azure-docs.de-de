---
title: Hochverfügbarkeit – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Konzepte für Hochverfügbarkeit und Notfallwiederherstellung
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/15/2021
ms.openlocfilehash: d708bb832673d424b1737ad0bea00716f1f3f278
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524852"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Hochverfügbarkeit in Azure Database for PostgreSQL – Hyperscale (Citus)

Hochverfügbarkeit (High Availability, HA) vermeidet Datenbankausfälle, indem Standbyreplikate jedes Knotens in einer Servergruppe verwaltet werden. Wenn ein Knoten ausfällt, schaltet Hyperscale (Citus) eingehende Verbindungen vom fehlerhaften Knoten auf den Standbyknoten um. Ein Failover erfolgt innerhalb weniger Minuten, und höher gestufte Knoten verfügen durch die synchrone PostgreSQL-Streamingreplikation immer über aktuelle Daten.

Auch ohne aktivierte Hochverfügbarkeit verfügt jeder Knoten Hyperscale (Citus) über einen eigenen lokal redundanten Speicher (LRS) mit drei synchronen Replikaten, die vom Azure Storage-Dienst gewartet werden.  Wenn ein einzelner Replikatfehler vor liegt, wird er vom Azure Storage-Dienst erkannt und transparent neu erstellt. Informationen zur Dauerhaftigkeit des LRS-Speichers finden Sie auf [dieser Seite](../storage/common/storage-redundancy.md#summary-of-redundancy-options) unter Metriken.

Wenn Hochverfügbarkeit *aktiviert ist,* führt Hyperscale (Citus) einen Standbyknoten für jeden primären Knoten in der Servergruppe aus. Der primäre Server und sein Standbyserver verwenden die synchrone PostgreSQL-Replikation. Diese Replikation ermöglicht Kunden eine vorhersagbare Ausfallzeit, wenn ein primärer Knoten ausfällt. Kurz gesagt erkennt unser Dienst einen Fehler auf primären Knoten und führt ein Failover auf Standbyknoten ohne Datenverlust aus.

Um die Vorteile von Hochverfügbarkeit auf dem Koordinatorknoten zu nutzen, müssen Datenbankanwendungen getrennte Verbindungen und fehlerhafte Transaktionen erkennen und Wiederholungsvorgänge für diese ausführen. Auf den höher gestuften neuen Koordinatorknoten kann mit der gleichen Verbindungszeichenfolge zugegriffen werden.

Die Wiederherstellung kann in drei Phasen unterteilt werden: Erkennung, Failover und vollständige Wiederherstellung.  Hyperscale (Citus) führt regelmäßige Integritätsprüfungen für jeden Knoten aus, und nach vier fehlgeschlagenen Prüfungen wird festgelegt, dass ein Knoten ausgefallen ist. Hyperscale (Citus) stuft dann einen Standbyknoten auf den primären Knotenstatus höher (Failover) und stellt einen neuen zukünftigen Standbyknoten bereit.
Die Streamingreplikation beginnt, sodass der neue Knoten auf den neuesten Stand gebracht wird.  Nachdem alle Daten repliziert wurden, hat der Knoten die vollständige Wiederherstellung erreicht.

### <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Hochverfügbarkeit](howto-hyperscale-high-availability.md) in einer Servergruppe mit Hyperscale (Citus) aktivieren.

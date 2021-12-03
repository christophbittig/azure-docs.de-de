---
title: Konzepte für die Verteilung von Daten und horizontales Skalieren mit einer Arc-fähigen PostgreSQL-Hyperscale-Servergruppe
titleSuffix: Azure Arc-enabled data services
description: Konzepte für die Verteilung von Daten mit einer Arc-fähigen PostgreSQL-Hyperscale-Servergruppe
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 06/02/2021
ms.topic: how-to
ms.openlocfilehash: aee8700274d074d94b6f6f8e1e153f256cb6e158
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294198"
---
# <a name="concepts-for-distributing-data-with-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Konzepte für die Verteilung von Daten mit einer Arc-fähigen PostgreSQL-Hyperscale-Servergruppe

In diesem Artikel werden zentrale Konzepte erläutert, die wichtig sind, um den größtmöglichen Nutzen aus PostgreSQL Hyperscale mit Azure Arc-Unterstützung ziehen zu können.
Die Links zu den Artikeln unten verweisen auf die Konzepte, die für Azure Database for PostgreSQL Hyperscale (Citus) erläutert werden. Es handelt sich um die gleiche Technologie wie bei PostgreSQL Hyperscale-Servergruppen mit Azure Arc-Unterstützung, sodass die gleichen Konzepte und Perspektiven gelten.

**Worin besteht der Unterschied?**
- _Azure Database for PostgreSQL Hyperscale (Citus)_

Dies ist der Hyperscale-Formfaktor der Postgres-Datenbank-Engine, der als Database-as-a-Service in Azure (PaaS) verfügbar ist. Er wird von der Citus-Erweiterung unterstützt, die die Hyperscale-Umgebung ermöglicht. In diesem Formfaktor wird der Dienst in den Microsoft-Rechenzentren ausgeführt und von Microsoft betrieben.

- _Azure Arc-fähiges PostgreSQL Hyperscale_

Dies ist der Hyperscale-Formfaktor der Postgres-Datenbank-Engine, der mit dem Datendienst mit Azure Arc-Unterstützung angeboten wird. In diesem Formfaktor stellen unsere Kunden die Infrastruktur bereit, die die Systeme hostet, und betreiben sie.

Im Anschluss finden Sie eine Zusammenfassung der zentralen Konzepte für PostgreSQL Hyperscale mit Azure Arc-Unterstützung:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>Knoten und Tabellen
Es ist wichtig, mit den folgenden Konzepten vertraut zu sein, um den größtmöglichen Nutzen aus PostgreSQL Hyperscale mit Azure Arc-Unterstützung ziehen zu können:
- Spezialisierte Postgres-Knoten in PostgreSQL Hyperscale mit Azure Arc-Unterstützung: Koordinator und Worker
- Tabellentypen: verteilte Tabellen, Verweistabellen und lokale Tabellen
- Shards

Weitere Informationen finden Sie unter [Knoten und Tabellen in Azure Database for PostgreSQL – Hyperscale (Citus)](../../postgresql/concepts-hyperscale-nodes.md). 

## <a name="determine-the-application-type"></a>Ermitteln des Anwendungstyps
Die eindeutige Identifizierung des Anwendungstyps, den Sie erstellen, ist wichtig. Warum? Weil das Ausführen effizienter Abfragen für eine PostgreSQL Hyperscale-Servergruppe mit Azure Arc-Unterstützung erfordert, dass die Tabellen ordnungsgemäß auf die Server verteilt sind. Die empfohlene Verteilung variiert abhängig vom Anwendungstyp und den jeweiligen Abfragemustern. Es gibt im Wesentlichen zwei Arten von Anwendungen, die gut mit Postgres Hyperscale mit Azure Arc-Unterstützung funktionieren:
- Mehrinstanzenfähige Anwendungen
- Echtzeitanwendungen

Der erste Schritt bei der Datenmodellierung besteht darin, herauszufinden, welche davon Ihrer Anwendung am ähnlichsten ist.

Einzelheiten dazu finden Sie unter [Ermitteln des Anwendungstyps](../../postgresql/concepts-hyperscale-app-type.md).


## <a name="choose-a-distribution-column"></a>Auswählen einer Verteilungsspalte
Warum sollte eine verteilte Spalte ausgewählt werden?

Dies ist eine der wichtigsten Modellierungsentscheidungen, die Sie treffen. PostgreSQL Hyperscale mit Azure Arc-Unterstützung speichert Zeilen basierend auf dem Wert der zugehörigen Verteilungsspalte in Shards. Durch die richtige Wahl werden verwandte Daten in denselben physischen Knoten gruppiert, was zu schnellen Abfragen führt und Unterstützung für alle SQL-Funktionen hinzufügt. Durch eine falsche Auswahl wird das System langsam ausgeführt und unterstützt nicht alle SQL-Funktionen auf allen Knoten. In diesem Artikel erhalten Sie Tipps für die Verteilungsspalte für die beiden häufigsten Hyperscale-Szenarien.

Einzelheiten dazu finden Sie unter[Auswählen von Verteilungsspalten](../../postgresql/concepts-hyperscale-choose-distribution-column.md).


## <a name="table-colocation"></a>Tabellenzusammenstellung

Colocation bedeutet, verwandte Informationen zusammen auf denselben Knoten zu speichern. Abfragen können schnell erfolgen, wenn alle erforderlichen Daten ohne Netzwerkdatenverkehr verfügbar sind. Durch das Zusammenstellen verwandter Daten auf unterschiedlichen Knoten können Abfragen effizient parallel auf den einzelnen Knoten ausgeführt werden.

Weitere Informationen finden Sie unter [Tabellen Zusammenstellung](../../postgresql/concepts-hyperscale-colocation.md).


## <a name="next-steps"></a>Nächste Schritte
- [Informationen zum Erstellen von PostgreSQL Hyperscale mit Azure Arc-Unterstützung](create-postgresql-hyperscale-server-group.md)
- [Informationen zum Aufskalieren von PostgreSQL Hyperscale-Servergruppen mit Azure Arc-Unterstützung, die in Ihrem Arc-Datencontroller erstellt wurden](scale-out-in-postgresql-hyperscale-server-group.md)
- [Informationen zu Data Services mit Azure Arc-Unterstützung](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Weitere Informationen zu Azure Arc](https://aka.ms/azurearc)

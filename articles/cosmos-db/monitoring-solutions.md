---
title: Überwachen von Azure Cosmos DB mithilfe von Überwachungstools von Drittanbietern
description: In diesem Artikel wird beschrieben, wie Drittanbietertools bei der Überwachung von Cosmos DB helfen können.
author: manishmsfte
ms.author: mansha
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/28/2021
ms.openlocfilehash: b78d6a4c92ad96c15d55332aac320bad09c58c1e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354764"
---
# <a name="monitoring-azure-cosmos-db-using-third-party-solutions"></a>Überwachen von Azure Cosmos DB mithilfe von Drittanbieterlösungen
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Neben Azure Monitor können Sie auch Überwachungslösungen von Drittanbietern verwenden, um Ihre Cosmos DB-Instanzen zu überwachen.

> [!IMPORTANT] 
> Die in diesem Artikel genannten Lösungen dienen nur zu Informationszwecken, sie gehören dem jeweiligen Besitzer. Es wird Benutzern empfohlen, die Lösungen umfassend zu evaluieren und dann die am besten geeignete Lösung auszuwählen.

## <a name="datadog"></a>Datadog
{Unterstützt: SQL-API, Azure Cosmos DB-API für MongoDB, Gremlin-API, Cassandra-API und Tabellen-API}

[Datadog](https://www.datadoghq.com/) ist eine vollständig vereinheitlichte Plattform, die u. a. die Überwachung der Infrastruktur, der Anwendungsleistung und der Benutzererfahrungen sowie die Protokollverwaltung umfasst. Durch das Zusammenführen von Daten aus jedem Tool und Dienst in Ihrem Unternehmen bietet Datadog eine zentrale Analysequelle für Problembehandlung, Leistungsoptimierung und teamübergreifende Zusammenarbeit.
In Datadog wird alles unter denselben Tags organisiert, sodass alle Daten, die für ein bestimmtes Problem relevant sind, automatisch korreliert werden. Durch die Beseitigung blinder Punkte verringert Datadog das Risiko, Fehler zu übersehen. Außerdem verringert es den Aufwand der laufenden Wartung von Diensten und beschleunigt digitale Transformationen.

Datadog erfasst mehr als 40 verschiedene Messgeräte- und Zählermetriken aus Cosmos DB, einschließlich des gesamten verfügbaren Speichers pro Region, der Anzahl der erstellten SQL-Datenbanken u. v. m. Diese Metriken werden über die Azure-Integration von Datadog gesammelt und sind auf der Plattform 40 % schneller verfügbar als bei anderen Produkten der Branche. Darüber hinaus bietet Datadog auch ein vordefiniertes Dashboard für Cosmos DB, das sofortige Einblicke in die Leistung von Cosmos DB-Instanzen bietet. Benutzer können Metriken auf Plattformebene wie die insgesamt verbrauchten Anforderungseinheiten sowie Metriken auf API-Ebene wie die Anzahl der erstellten Cassandra-Keyspaces visualisieren und damit ihre Cosmos DB-Nutzung besser verstehen.

Datadog wird von vielen Cosmos DB-Kunden verwendet, darunter:
- Maersk
- PWC 
- PayScale 
- AllScripts 
- Hearst



:::image type="content" source="./media/monitor-solutions/datadog-demo.gif" alt-text="Demo für Datadog" border="false":::
**Abbildung:** Datadog in Aktion

Hilfreiche Links:
- [Preisübersicht](https://www.datadoghq.com/pricing/)
- [Erste Schritte mit der 14-Tage-Testversion](https://www.datadoghq.com/free-datadog-trial/)


## <a name="dynatrace"></a>Dynatrace
{Unterstützt: SQL API und Azure Cosmos DB-API für MongoDB}

[Dynatrace](https://www.dynatrace.com/platform/) bietet Softwareintelligenz für die Cloud, um die Cloudkomplexität zu verringern und die digitale Transformation zu beschleunigen. Die umfassende Software Intelligence Platform von Dynatrace liefert automatische und intelligente Beobachtbarkeit im großen Stil und präzise Antworten auf Fragen zur Leistung und Sicherheit von Anwendungen, der zugrunde liegenden Infrastruktur und der Nutzung aller Benutzer. So können Teams Cloudvorgänge automatisieren, noch schneller noch bessere Software veröffentlichen und außergewöhnliche digitale Erfahrungen bereitstellen.  
Dynatrace nutzt die Mongo-API für das Erfassen und Bereitstellen von Cosmos DB-Metriken wie der Anzahl der Aufrufe und Antwortzeiten, die dann alle nach Aggregation, Befehlen und Lese- und Schreibvorgängen visualisiert werden.  Dabei werden Sie auch genau über sämtliche Datenbankanweisungen informiert, die in Ihrer Umgebung ausgeführt werden.  Schließlich können Sie mit der leistungsstarken [Davis AI Engine](https://www.dynatrace.com/davis) genau erkennen, welche Datenbankanweisung in welcher Datenbank die Grundursache einer Verschlechterung ist.

:::image type="content" source="./media/monitor-solutions/dynatrace-demo.gif" alt-text="Verschiedene Bildschirme von Dynatrace, auf denen Überwachungsinformationen für Cosmos DB bereitgestellt werden" border="false":::
**Abbildung:** Dynatrace in Aktion

### <a name="useful-links"></a>Nützliche Links

- [Kostenlose 15-Tage-Testversion von Dynatrace](https://www.dynatrace.com/trial)
- [Starten aus Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dynatrace.dynatrace-managed)
- [Dokumentation zum Überwachen von Cosmos DB mit Azure Monitor](https://www.dynatrace.com/support/help/technology-support/cloud-platforms/microsoft-azure-services/set-up-integration-with-azure-monitor/?_ga=2.184080354.559899881.1623174355-748416177.1603817475)
- [Cosmos DB: Details zur Dynatrace-Integration](https://www.dynatrace.com/news/blog/azure-services-explained-part-4-azure-cosmos-db/?_ga=2.185016301.559899881.1623174355-748416177.1603817475)
- [Dynatrace-Überwachung für Azure-Datenbanken](https://www.dynatrace.com/technologies/azure-monitoring/azure-database-performance/)
- [Übersicht über Dynatrace für Azure-Lösungen](https://www.dynatrace.com/technologies/azure-monitoring/)
- [Lösungspartner](https://www.dynatrace.com/partners/solution-partners/)

## <a name="next-steps"></a>Nächste Schritte
- [Referenz zur Überwachung von Daten in Cosmos DB](./monitor-cosmos-db-reference.md)

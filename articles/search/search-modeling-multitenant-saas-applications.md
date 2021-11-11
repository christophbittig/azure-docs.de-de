---
title: Mehrinstanzenfähigkeit und Inhaltsisolierung
titleSuffix: Azure Cognitive Search
description: Informationen zu gängigen Entwurfsmustern für mehrinstanzenfähige SaaS-Anwendungen bei Verwendung der kognitiven Azure-Suche.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 057c06b2d3b83b49448e2ec6edd0b1140a324375
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422518"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und die kognitive Azure-Suche

Eine mehrinstanzenfähige Anwendung bietet dieselben Dienste und Funktionen einer beliebigen Anzahl von Mandanten, die die Daten der anderen Mandaten nicht anzeigen oder mit diesen gemeinsam nutzen können. In diesem Dokument werden Strategien zur Isolierung von Mandanten für mehrinstanzenfähige Anwendungen erläutert, die mit der kognitiven Azure-Suche erstellt werden.

## <a name="azure-cognitive-search-concepts"></a>Konzepte der kognitiven Azure-Suche

[Azure Cognitive Search](search-what-is-azure-search.md) ist eine Search-as-a-Service-Lösung, die es Entwicklern ermöglicht, Anwendungen umfassende Suchfunktionen hinzuzufügen, ohne Infrastruktur verwalten oder Experte für das Abrufen von Informationen werden zu müssen. Daten werden in den Dienst hochgeladen und dann in der Cloud gespeichert. Mithilfe einfacher an die API der kognitiven Azure-Suche gerichteter Anforderungen können die Daten geändert und durchsucht werden. 

### <a name="search-services-indexes-fields-and-documents"></a>Suchdienste, Indizes, Felder und Dokumente

Bevor wir näher auf Entwurfsmuster eingehen, müssen Sie einige grundlegende Konzepte kennen und verstehen.

Zur Verwendung der kognitiven Azure-Suche muss ein *Suchdienst* abonniert werden. Die in die kognitive Azure-Suche hochgeladenen Daten werden in einem *Index* innerhalb des Suchdiensts gespeichert. In einem einzelnen Dienst kann es mehrere Indizes geben. Analog zu den vertrauten Konzepten von Datenbanken kann der Suchdienst mit einer Datenbank verglichen werden, während die Indizes im Dienst mit Tabellen in einer Datenbank vergleichbar sind.

Jeder Index in einen Suchdienst hat ein eigenes Schema, das mithilfe verschiedener anpassbarer *Felder* definiert wird. Daten werden einem Index der kognitiven Azure-Suche in Form einzelner *Dokumente* hinzugefügt. Jedes Dokument muss in einen bestimmten Index hochgeladen werden und dem Schema dieses Indexes entsprechen. Beim Durchsuchen von Daten mithilfe der kognitiven Azure-Suche werden die Volltextsuchabfragen auf einen bestimmten Index angewandt.  Wiederum analog zu Datenbanken können Felder mit den Spalten einer Tabelle und Dokumente mit Zeilen verglichen werden.

### <a name="scalability"></a>Skalierbarkeit

Ein Dienst der kognitiven Azure-Suche im [Tarif „Standard“](https://azure.microsoft.com/pricing/details/search/) kann in zwei Bereichen skaliert werden: Speicherung und Verfügbarkeit.

+ *Partitionen* können hinzugefügt werden, um die Speicherkapazität eines Suchdienst zu erhöhen.
+ *Replikate* können einem Dienst hinzugefügt werden, um den Durchsatz von Anforderungen zu erhöhen, die ein Suchdienst verarbeiten kann.

Das Hinzufügen und Entfernen von Partitionen und Replikaten nach Belieben ermöglicht, dass die Kapazität des Suchdiensts mit der Datenmenge und dem Datenverkehr entsprechend den Anforderungen der Anwendung wachsen kann. Damit der Suchdienst eine [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)für Lesevorgänge erfüllen kann, sind zwei Replikate erforderlich. Damit der Dienst eine [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)für Lese- und Schreibvorgänge erfüllen kann, sind drei Replikate erforderlich.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Dienst- und Indexgrenzwerte für die kognitive Azure-Suche

Die kognitive Azure-Suche bietet verschiedene [Tarife](https://azure.microsoft.com/pricing/details/search/) mit unterschiedlichen [Grenzwerten und Kontingenten](search-limits-quotas-capacity.md). Diese Grenzwerte gelten auf Dienst-, Index- und Partitionsebene.

|  | Basic | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| **Maximale Anzahl von Replikaten pro Dienst** |3 |12 |12 |12 |12 |
| **Maximale Anzahl von Partitionen pro Dienst** |1 |12 |12 |12 |3 |
| **Maximale Anzahl von Sucheinheiten (Replikate x Partitionen) pro Dienst** |3 |36 |36 |36 |36 (max. 3 Partitionen) |
| **Maximale Speicherkapazität pro Dienst** |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| **Maximale Speicherkapazität pro Partition** |2 GB |25 GB |100 GB |200 GB |200 GB |
| **Maximale Anzahl von Indizes pro Dienst** |5 |50 |200 |200 |3\.000 (max. 1.000 Indizes/Partition) |

#### <a name="s3-high-density"></a>S3 High Density

Der Tarif „S3“ der kognitiven Azure-Suche bietet als Option einen HD-Modus (High Density), der speziell auf Szenarien mit mehreren Mandanten ausgelegt ist. In vielen Fällen ist es notwendig, eine große Zahl von kleineren Mandanten unter einem einzelnen Dienst zu unterstützen, um die Vorteile der Einfachheit und Kosteneffizienz nutzen zu können.

Mit S3 HD können die vielen kleinen Indizes unter der Verwaltung eines einzelnen Suchdiensts gepackt werden, indem die Möglichkeit zum Aufskalieren von Indizes mithilfe von Partitionen gegen die Möglichkeit eingetauscht wird, eine größere Anzahl von Indizes in einem einzelnen Dienst zu hosten.

Ein S3-Dienst dient zum Hosten einer bestimmten Anzahl von Indizes (maximal 200) und ermöglicht die horizontale Skalierung der einzelnen Indizes, wenn dem Dienst neue Partitionen hinzugefügt werden. Durch Hinzufügen von Partitionen zu S3 HD-Diensten wird die maximale Anzahl von Indizes erhöht, die vom Dienst gehostet werden können. Die ideale maximale Größe für einen einzelnen S3 HD-Index beträgt ca. 50–80 GB, das System erzwingt jedoch keine feste Größenbeschränkung für jeden Index.

## <a name="considerations-for-multitenant-applications"></a>Aspekte bei mehrinstanzenfähigen Anwendungen

Mehrinstanzenfähige Anwendungen müssen Ressourcen gleichmäßig den Mandanten zuteilen und für ein Maß an Datenschutz zwischen diesen sorgen. Beim Entwerfen der Architektur einer solchen Anwendung sind verschiedene Aspekte zu beachten:

+ *Mandantenisolierung:* Anwendungsentwickler müssen geeignete Maßnahmen ergreifen, um sicherzustellen, dass Mandanten keinen nicht autorisierten oder unerwünschten Zugriff auf die Daten anderer Mandanten haben. Abgesehen vom Datenschutz erfordern Strategien für die Isolierung von Mandanten eine effektive Verwaltung gemeinsam genutzter Ressourcen und Schutz vor „lauten“, in diesem Fall überaus aktiven, Nachbarn.

+ *Cloudressourcenkosten:* Wie andere Anwendungen auch müssen Softwarelösungen als Teil einer mehrinstanzenfähigen Anwendung wettbewerbsfähig bleiben.

+ *Einfacher Betrieb:* Bei der Entwicklung einer mehrinstanzenfähigen Architektur sind Betrieb und Komplexität der Anwendung ein wichtiger Aspekt. Die kognitive Azure-Suche bietet eine [SLA von 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

+ *Globale Verteilung:* Mehrinstanzenfähige Anwendungen müssen gegebenenfalls Anforderungen von Mandanten erfüllen, die weltweit verteilt sind.

+ *Skalierbarkeit:* Anwendungsentwickler müssen berücksichtigen, wie ein Kompromiss erreicht wird zwischen der Beibehaltung eines ausreichend niedrigen Grads an Anwendungskomplexität und dem Entwurf der Anwendung für eine Skalierung entsprechend der Anzahl der Mandanten sowie deren Daten und Workloads.

Die kognitive Azure-Suche bietet verschiedene Abgrenzungsmöglichkeiten, mit deren Hilfe die Daten und Workloads von Mandanten isoliert werden können.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Modellieren der Mehrinstanzenfähigkeit mit der kognitiven Azure-Suche

Bei einem mehrinstanzenfähigen Szenario nutzt der Anwendungsentwickler einen oder mehrere Suchdienste und verteilt die Mandanten auf Dienste, Indizes oder beides. Die kognitive Azure-Suche unterstützt beim Modellieren eines mehrinstanzenfähigen Szenarios mehrere Muster:

+ *Ein Index pro Mandant:* Jeder Mandant verfügt über einen eigenen Index in einem Suchdienst, der mit anderen Mandanten gemeinsam genutzt wird.

+ *Ein Dienst pro Mandant:* Jeder Mandant hat einen eigenen dedizierten -Azure Cognitive Search-Dienst und erreicht somit ein Höchstmaß an Trennung von Daten und Workloads.

+ *Kombination beider Muster:* Größeren, aktiveren Mandanten werden dedizierte Dienste zugewiesen, während kleineren Mandanten einzelne Indizes in gemeinsam genutzten Diensten zugeteilt werden.

## <a name="model-1-one-index-per-tenant"></a>Modell 1: Ein Index pro Mandant

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png" alt-text="Eine Abbildung des Index-pro-Mandant-Modells" border="false":::

Bei einem Index-pro-Mandant-Modell nutzen mehrere Mandanten einen einzelnen Dienst der kognitiven Azure-Suche, wobei jeder Mandant einen eigenen Index hat.

Die Daten der Mandanten sind voneinander isoliert, da alle Suchanforderungen und Dokumentvorgänge in der kognitiven Azure-Suche auf Indexebene erfolgen. Auf Anwendungsebene muss der Datenverkehr der verschiedenen Mandanten zu den ordnungsgemäßen Indizes geleitet werden, während auf Dienstebene Ressourcen für alle Mandanten verwaltet werden müssen.

Ein wesentliches Merkmal des Index-pro-Mandant-Modells ist die Fähigkeit des Anwendungsentwicklers, die abonnierte Kapazität eines Suchdienst für die Mandanten der Anwendung zu überschreiten. Wenn bei den Mandanten die Workload ungleichmäßig verteilt ist, kann die optimale Kombination von Mandanten auf die Indizes eines Suchdiensts verteilt werden. Der Zweck ist es, eine Anzahl sehr aktiver Mandanten mit intensiver Ressourcennutzung und zugleich eine große Menge weniger aktiver Mandanten zu unterstützen. Der Nachteil ist die Unfähigkeit des Modells, mit Situationen klar zu kommen, bei denen alle Mandanten gleichzeitig sehr aktiv sind.

Das Index-pro-Mandant-Modell bildet die Grundlage eines variablen Kostenmodells, bei dem ein gesamter Dienst der kognitiven Azure-Suche vorab gebucht und anschließend mit Mandanten aufgefüllt wird. Dies ermöglicht, dass freie Kapazität für Tests und kostenlose Konten zur Verfügung gestellt wird.

Für Anwendungen mit globaler Verteilung ist das Index-pro-Mandant-Modell u.U. nicht das effizienteste. Wenn die Mandanten einer Anwendung auf der ganzen Welt verteilt sind, ist ggf. ein separater Dienst für jede Region erforderlich, was möglicherweise zur Verdopplung der jeweiligen Kosten führen kann.

Die kognitive Azure-Suche ermöglicht die Skalierung von einzelnen Indizes sowie der Gesamtanzahl von Indizes. Bei Wahl eines entsprechenden Tarifs können Partitionen und Replikate dem gesamte Suchdienst hinzugefügt werden, wenn ein einzelner Index im Dienst in Bezug auf Speicherung oder Datenverkehr zu groß geworden ist.

Wenn die Gesamtanzahl der Indizes für einen einzelnen Dienst zu groß wird, muss ein anderer Dienst bereitgestellt werden, um die neuen Mandanten zu unterstützen. Wenn Indizes zwischen Suchdiensten verschoben werden müssen, weil neue Dienste hinzugefügt werden, müssen die Daten aus dem Index manuell in einen anderen Index kopiert werden, da das Verschieben eines Index in der kognitiven Azure-Suche nicht zulässig ist.

## <a name="model-2-one-service-per-tenant"></a>Modell 2: Ein Dienst pro Mandant

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png" alt-text="Eine Abbildung des Dienst-pro-Mandant-Modells" border="false":::

Bei einer Dienst-pro-Mandant-Architektur hat jeder Mandant einen eigenen Suchdienst.

Bei diesem Modell erzielt die Anwendung den Maximalgrad an Isolierung für die Mandanten. Jeder Dienst erhält dedizierten Speicher und Durchsatz für die Bewältigung von Suchanfragen und getrennte API-Schlüssel.

Für Anwendungen, bei denen Mandanten weltweit verteilt sind oder sich die Workload von Mandant zu Mandant nur geringfügig unterscheidet, ist das Dienst-pro-Mandant-Modell eine geeignete Wahl, da Ressourcen zur Bewältigung der Workloads der verschiedenen Mandanten nicht gemeinsam genutzt werden.

Ein Dienst-pro-Mandant-Modell bietet außerdem den Vorteil eines vorhersagbaren, festen Kostenmodells. Es sind keine Vorabinvestitionen in einen gesamten Suchdienst erforderlich, der mit Mandanten gefüllt werden muss. Allerdings sind die Kosten pro Mandant höher als beim Index-pro-Mandant-Modell.

Das Dienst-pro-Mandant-Modell eignet sich besonders für Anwendungen mit global verteilten Nutzern. Bei geografisch verteilten Mandanten ist es einfach, jedem Mandanten in der entsprechenden Region einen Dienst zur Verfügung zu stellen.

Die Herausforderung bei der Skalierung dieses Musters entstehen, wenn einzelne Mandanten die Kapazitätsgrenzen ihres Diensts erreichen. Die kognitive Azure-Suche unterstützt derzeit keine Aktualisierung des Tarifs eines Suchdiensts, sodass alle Daten manuell in einen neuen Dienst kopiert werden müssen.

## <a name="model-3-hybrid"></a>Modell 3: Hybrid

Ein weiteres Muster für die Modellierung von Mehrinstanzenfähigkeit ist das Kombinieren des Index-pro-Mandant- und Dienst-pro-Mandant-Modells.

Durch Kombinieren der beiden Modelle können die größten Mandanten einer Anwendung dedizierte Dienste in Anspruch nehmen, während dem großen Rest weniger aktiver, kleinerer Mandanten die Indizes in einem gemeinsam genutzten Dienst zur Verfügung stehen. Dieses Modell stellt sicher, dass der Dienst den größten Mandanten stets eine hohe Leistung bietet, während zugleich die kleineren Mandaten vor einer Beeinträchtigung durch diese meist sehr aktiven Mandaten geschützt werden.

Allerdings erfordert das Umsetzen dieser Strategie Weitsicht bei der Vorhersage, welche Mandanten einen dedizierten und welche einen Index in einem gemeinsamen genutzt Dienst benötigen. Die Komplexität der Anwendung erhöht sich, da zwei mehrinstanzenfähige Modelle verwaltet werden müssen.

## <a name="achieving-even-finer-granularity"></a>Erreichen einer feineren Granularität

Die oben genannten Entwurfsmuster zum Modellieren mehrinstanzenfähiger Szenarien in der kognitiven Azure-Suche setzen einen einheitlichen Umfang voraus, bei dem jeder Mandant eine ganze Instanz einer Anwendung nutzt. Anwendungen müssen jedoch mitunter mit vielen kleineren Umfängen zurechtkommen.

Wenn das Dienst-pro-Mandant- und Index-pro-Mandant-Modell nicht ausreichend kleine Umfänge darstellen, ist es möglich, einen Index zu modellieren, mit dem ein noch feinerer Grad an Granularität erreicht wird.

Damit sich ein einzelner Index für verschiedene Clientendpunkte anders verhält, kann einem Index ein Feld hinzugefügt werden, dass jedem möglichen Client einen bestimmten Wert zuweist. Immer wenn ein Client die kognitive Azure-Suche zum Abfragen oder Ändern eines Index aufruft, gibt der Code aus der Clientanwendung den entsprechenden Wert für dieses Feld mithilfe der [Filterfunktion](./query-odata-filter-orderby-syntax.md) der kognitiven Azure-Suche zum Zeitpunkt der Abfrage an.

Mithilfe dieser Methode lassen sich eine Unterstützung getrennter Benutzerkonten und Berechtigungsstufen sowie sogar vollständig getrennter Anwendungen erreichen.

> [!NOTE]
> Der oben beschriebene Ansatz der Konfiguration eines einzelnen Indizes für mehrere Mandanten hat Auswirkungen auf die Relevanz der Suchergebnisse. Die Relevanzbewertungen der Suche werden auf Indexebene berechnet, nicht auf Mandantenebene. Dadurch werden Daten für alle Mandanten in die den Relevanzbewertungen zugrunde liegenden Statistiken einbezogen, z.B. die Ausdruckshäufigkeit.
>

## <a name="next-steps"></a>Nächste Schritte

Die kognitive Azure-Suche ist eine hervorragende Wahl für viele Anwendungen. Berücksichtigen Sie beim Bewerten der verschiedenen Entwurfsmuster für mehrinstanzenfähige Anwendungen die [verschiedenen Tarife](https://azure.microsoft.com/pricing/details/search/) und zugehörigen [Dienstgrenzen](search-limits-quotas-capacity.md), um die kognitive Azure-Suche perfekt an Anwendungsworkloads und Architekturen sämtlicher Größen anzupassen.

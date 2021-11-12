---
title: Schätzen der Kosten
titleSuffix: Azure Cognitive Search
description: Hier finden Sie Informationen zu fakturierbaren Ereignissen, zum Preismodell und Tipps für die Verwaltung der Kosten der Ausführung eines Cognitive Search-Diensts.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: 21b12da25ae2baf7e31ad12af7b5c346b64bc3ac
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577230"
---
# <a name="estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Schätzen und Verwalten der Kosten eines Azure Cognitive Search-Diensts

In diesem Artikel erfahren Sie mehr über das Preismodell, fakturierbare Ereignisse und erhalten Tipps zur Verwaltung der Kosten der Ausführung eines Azure Cognitive Search-Diensts.

## <a name="pricing-model"></a>Preismodell

Die Skalierbarkeitsarchitektur in Azure Cognitive Search basiert auf flexiblen Kombinationen von Replikaten und Partitionen, sodass Sie die Kapazität variieren können – je nachdem, ob Sie mehr Abfrage- oder mehr Indizierungsleistung benötigen, und zahlen nur für das, was Sie benötigen.

Die Menge der von Ihrem Suchdienst verwendeten Ressourcen, multipliziert mit dem Abrechnungssatz, der von der Dienstebene festgelegt wird, bestimmt die Kosten für die Ausführung des Diensts. Kosten und Kapazität sind eng aneinander gebunden. Wenn Sie die Kosten schätzen, sollten Sie die erforderliche Kapazität für die Ausführung Ihrer Indizierungs- und Abfrageworkloads kennen, um einschätzen zu können, welche Kosten entstehen.

Zu Abrechnungszwecken gibt es zwei einfache Formeln, die Sie beachten sollten:

| Formel | BESCHREIBUNG |
|---------|-------------|
| **R x P = SU** | Die Anzahl der verwendeten Replikate, multipliziert mit der Anzahl der verwendeten Partitionen, entspricht der Anzahl von *Sucheinheiten* (SU), die von einem Dienst verwendet werden. Eine SU ist eine Ressourceneinheit und kann entweder eine Partition oder ein Replikat sein. |
| **SU * Abrechnungsrate = monatliche Ausgabe** | Die Anzahl der SUs multipliziert mit der Abrechnungsrate der Ebene, auf der Sie den Dienst bereitgestellt haben, ist die primäre Determinante ihrer gesamten monatlichen Rechnung. Einige Features oder Workloads weisen Abhängigkeiten von anderen Azure-Diensten auf, die die Kosten für Ihre Lösung auf Abonnementebene erhöhen können. Im folgenden Abschnitt zu fakturierbaren Ereignissen finden Sie Features, die Ihrer Rechnung hinzugefügt werden können. |

Jeder Dienst beginnt mit mindestens einer SU (einem Replikat multipliziert mit einer Partition). Die maximale Anzahl von SUs für einen Dienst beträgt 36. Dieser Höchstwert kann auf verschiedene Weise erreicht werden: beispielsweise mit 6 Partitionen x 6 Replikaten oder mit 3 Partitionen x 12 Replikaten. Üblicherweise werden weniger als die Gesamtkapazität an SUs verwendet (3 Replikate x 3 Partitionen werden beispielsweise als 9 SUs abgerechnet). Gültige Kombinationen finden Sie in der Tabelle [Partitions- und Replikatskombinationen](search-capacity-planning.md#chart).

Der Preis wird stündlich pro SU abgerechnet. Dabei steigt der Preis mit jedem höheren Tarif an. Höhere Tarife enthalten größere und schnellere Partitionen, was zu einem insgesamt höheren Stundensatz für den jeweiligen Tarif führt. Die Preise für die einzelnen Tarife finden Sie auf der Seite [Azure Search – Preise](https://azure.microsoft.com/pricing/details/search/).

Die meisten Kunden schalten nur einen Teil der Gesamtkapazität online und halten den Rest in Reserve. Die Anzahl der Partitionen und Replikate, die Sie online schalten, bestimmt (mit der SU-Formel berechnet) den stündlichen Preis. 

## <a name="billable-events"></a>Abrechenbare Ereignisse

Für eine auf der kognitiven Azure-Suche basierende Lösung können folgende Kosten anfallen:

+ [Kosten für den Dienst](#service-costs) selbst, der rund um die Uhr mit einer minimalen Konfiguration (eine Partition und ein Replikat) im Basistarif ausgeführt wird. Sie können sich dies als die Fixkosten für die Ausführung des Diensts vorstellen.

+ Hinzufügen von Kapazität (Replikate oder Partitionen), wobei die Kosten in Inkrementen der abrechenbaren Rate steigen. Wenn Hochverfügbarkeit eine geschäftliche Anforderung ist, benötigen Sie drei Replikate.

+ Bandbreitengebühren (ausgehende Datenübertragung)

+ Für bestimmte Premium-Funktionen oder Features erforderliche Add-On-Dienste:

  + KI-Anreicherung mit gebührenpflichtigen Skills (erfordert [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)). Die Bildextraktion ist auch gebührenpflichtig.
  + Wissensspeicher (erfordert [Azure Storage](https://azure.microsoft.com/pricing/details/storage/)). Speichervorgänge nach Indexer werden abgerechnet.
  + Inkrementelle Anreicherung (erfordert [Azure Storage](https://azure.microsoft.com/pricing/details/storage/), gilt für KI-Anreicherung)
  + Kundenseitig verwaltete Schlüssel und doppelte Verschlüsselung (erfordert [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + Private Endpunkte für ein Zugriffsmodell ohne Internet (erfordert [Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/))
  + Die semantische Suche ist ein Premium-Feature im Tarif „Standard“ (Kosten finden Sie auf der [Seite mit der Preisübersicht für Cognitive Search](https://azure.microsoft.com/pricing/details/search/)). Sie können die [semantische Suche deaktivieren](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchsemanticsearch), um eine versehentliche Verwendung zu verhindern.

### <a name="service-costs"></a>Dienstkosten

Im Unterschied zu virtuellen Computern oder anderen Ressourcen, die angehalten werden können, um Gebühren zu vermeiden, ist ein Dienst für die kognitive Azure-Suche immer auf der Hardware verfügbar, die für Ihre ausschließliche Nutzung bestimmt ist. Daher ist die Erstellung eines Diensts ein abrechenbares Ereignis, das mit dem Erstellen des Diensts beginnt und mit dem Löschen des Diensts endet. 

Die Mindestgebühr fällt zum abrechenbaren Preis für die erste Sucheinheit (ein Replikat × eine Partition) an. Dieser Betrag gilt für die Lebensdauer des Diensts, da der Dienst nicht mit weniger als dieser Konfiguration ausgeführt werden kann. 

Über dieses Minimum hinaus können Sie Replikate und Partitionen unabhängig voneinander hinzufügen. Inkrementelle Kapazitätserweiterungen durch Replikate und Partitionen erhöhen die Abrechnung basierend auf der folgenden Formel: **(Replikate × Partitionen × Abrechnungsrate)** . Der abgerechnete Preis hängt dabei von Ihrem ausgewählten Tarif ab.

Wenn Sie Ihre Kosten für eine Suchlösung abschätzen möchten, bedenken Sie, dass sich Preis und Kapazität nicht linear verhalten (bei Verdoppelung der Kapazität werden die Kosten im gleichen Tarif mehr als verdoppelt). Außerdem kann der Wechsel zu einem höheren Tarif zu einer besseren und schnelleren Leistung bei etwa gleichem Preis führen. Weitere Informationen und ein Beispiel finden Sie unter [Upgrade auf einen Standard-S2-Tarif](search-performance-tips.md#tip-upgrade-to-a-standard-s2-tier).

### <a name="bandwidth-charges"></a>Bandbreitengebühren

Die Verwendung von [Indexern](search-indexer-overview.md) kann sich auf die Abrechnung auswirken, wenn sich die Azure-Datenquelle in einer anderen Region als Azure Cognitive Search befindet. In diesem Szenario könnten Kosten für das Verschieben ausgehender Daten aus der Azure-Datenquelle in Azure Cognitive Search entstehen. Weitere Informationen finden Sie auf den Preisseiten der jeweiligen Azure-Datenplattform.

Sie können die Gebühren für ausgehende Daten vollständig vermeiden, wenn Sie den Dienst für die kognitive Azure-Suche in derselben Region wie Ihre Daten erstellen. Die folgenden Informationen stammen von der Seite [Preisübersicht Bandbreite](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Eingehende Daten: Microsoft stellt keine Gebühren für in beliebige Azure-Dienste eingehende Daten in Rechnung. 

+ Ausgehende Daten:  „Ausgehende Daten“ bezieht sich auf Abfrageergebnisse. Bei Cognitive Search fallen keine Gebühren für ausgehende Daten an, aber Ausgangsgebühren von Azure sind möglich, wenn sich Dienste in unterschiedlichen Regionen befinden.

  Diese Gebühren sind nicht Teil Ihrer eigentlichen Rechnung für die kognitive Azure-Suche. Sie werden hier erwähnt, weil diese Kosten sich in Ihrer Gesamtrechnung widerspiegeln könnten, wenn Sie Ergebnisse an andere Regionen oder Nicht-Azure-Apps senden.

### <a name="ai-enrichment-with-cognitive-services"></a>KI-Anreicherung mit Cognitive Services

Für die [KI-Anreicherung](cognitive-search-concept-intro.md), die gebührenpflichtige Skills verwenden, sollten Sie eine [abrechenbare Azure Cognitive Services-Ressource](cognitive-search-attach-cognitive-services.md) in derselben Region wie Azure Cognitive Search im Tarif „S0“ für die nutzungsbasierte Bezahlung der Verarbeitung anfügen. Durch das Anfügen von Cognitive Services fallen keine festen Kosten an. Sie bezahlen nur für die benötigte Verarbeitung.

| Vorgang | Auswirkungen auf die Abrechnung |
|-----------|----------------|
| Dokumententschlüsselung, Textextraktion | Kostenlos |
| Dokumententschlüsselung, Bildextraktion | Die Abrechnung erfolgt entsprechend der Anzahl von Bildern, die aus den Dokumenten extrahiert werden. In einer [Indexerkonfiguration](/rest/api/searchservice/create-indexer#indexer-parameters) löst der Parameter **imageAction** die Bildextraktion aus. Ist **imageAction** auf „None“ festgelegt (Standardeinstellung), fallen keine Gebühren für die Bildextraktion an. Informationen zu den Gebühren für die Bildextraktion finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/search/). |
| [Integrierte Skills](cognitive-search-predefined-skills.md), die auf Cognitive Services basieren | Wird zum gleichen Preis abgerechnet, als ob Sie die Aufgabe direkt mithilfe von Cognitive Services ausgeführt hätten. Sie können 20 Dokumente pro Indexer und Tag kostenlos verarbeiten. Größere oder häufigere Workloads erfordern einen Schlüssel. |
| [Integrierte Skills](cognitive-search-predefined-skills.md), die keine Anreicherungen hinzufügen | Keine. Zu den nicht gebührenpflichtigen Hilfsprogrammskills zählen „Bedingt“, „Shaper“, „Textzusammenführung“, „Textaufteilung“. Hat keine Auswirkungen auf die Abrechnung, weist keine Cognitive Services-Schlüsselanforderungen auf, und es gilt keine Begrenzung auf 20 Dokumente. |
| Benutzerdefinierte Qualifikationen | Bei einer benutzerdefinierten Qualifikation handelt es sich um die von Ihnen bereitgestellte Funktionalität. Die Kosten für die Verwendung einer benutzerdefinierten Qualifikation hängen ganz davon ab, ob mit dem benutzerdefinierten Code andere gebührenpflichtige Dienste aufgerufen werden.  Weist keine Cognitive Services-Schlüsselanforderungen auf, und es gilt keine Begrenzung auf 20 Dokumente für benutzerdefinierte Skills.|
| [Benutzerdefinierte Entitätssuche](cognitive-search-skill-custom-entity-lookup.md) | Berechnet durch Azure Cognitive Search. Ausführlichere Informationen finden Sie auf der Seite mit der [Preisübersicht](https://azure.microsoft.com/pricing/details/search/#pricing). |

> [!TIP]
> Die [inkrementelle Anreicherung (Vorschau)](cognitive-search-incremental-indexing-conceptual.md) senkt die Kosten für die Skillsetverarbeitung, indem Anreicherungen, die von Änderungen an einem Skillset nicht betroffen sind, zwischengespeichert und wiederverwendet werden. Die Zwischenspeicherung erfordert Azure Storage (siehe [Preise](https://azure.microsoft.com/pricing/details/storage/blobs/), aber die kumulativen Kosten für die Ausführung von Skillsets sind niedriger, wenn vorhandene Anreicherungen wiederverwendet werden können).

## <a name="tips-for-managing-costs"></a>Tipps zum Verwalten von Kosten

Die folgenden Anleitungen können Ihnen helfen, Kosten zu senken oder effektiver zu verwalten:

+ Erstellen Sie alle Ressourcen in derselben Region oder in möglichst wenigen Regionen, um Bandbreitengebühren zu minimieren oder auszuschließen.

+ Führen Sie alle Dienste in einer Ressourcengruppe zusammen, z.B. kognitive Azure-Suche, Cognitive Services und alle anderen in Ihrer Lösung verwendeten Azure-Dienste. Navigieren Sie im Azure-Portal zu der Ressourcengruppe, und verwenden Sie die Befehle für **Kostenverwaltung**, um Einblicke in die tatsächlichen und projizierten Ausgaben zu erhalten.

+ Sie können Azure App Service für Ihre Front-End-Anwendung verwenden, sodass Anforderungen und Antworten innerhalb der Grenzen des Rechenzentrums bleiben.

+ Führen Sie die Hochskalierung für ressourcenintensive Vorgänge wie die Indizierung durch, und passen Sie die Skalierung anschließend für reguläre Abfrageworkloads wieder an. Beginnen Sie mit der Mindestkonfiguration für die kognitive Azure-Suche (eine SU bestehend aus einer Partition und einem Replikat), und überwachen Sie dann die Benutzeraktivität, um Verwendungsmuster zu identifizieren, die auf die Notwendigkeit einer höheren Kapazität hindeuten. Wenn ein vorhersagbares Muster vorliegt, können Sie möglicherweise die Skalierung mit der Aktivität synchronisieren (um dies zu automatisieren, müssen Sie Code schreiben).

+ Cost Management ist in die Azure-Infrastruktur integriert. Weitere Informationen zur Nachverfolgung von Kosten, Tools und APIs finden Sie unter [Was ist „Azure Cost Management + Abrechnung“?](../cost-management-billing/cost-management-billing-overview.md).

Das temporäre Herunterfahren eines Suchdiensts ist nicht möglich. Dedizierte Ressourcen werden für eine exklusive Nutzung während der gesamten Lebensdauer des Diensts ständig betrieben. Das Löschen eines Diensts ist endgültig und löscht auch die zugehörigen Daten.

Im Hinblick auf den Dienst selbst besteht die einzige Möglichkeit zur Kostenreduzierung darin, Replikate und Partitionen auf ein Niveau zu reduzieren, das immer noch eine akzeptable Leistung und [SLA-Konformität](https://azure.microsoft.com/support/legal/sla/search/v1_0/) bietet, oder einen Dienst mit einem niedrigeren Tarif zu erstellen. (Die Stundensätze von S1 sind niedriger als die von S2 oder S3.) Angenommen, Sie stellen einen Dienst am unteren Ende Ihrer voraussichtlichen Auslastungen bereit. Wenn dieser Dienst Ihre Anforderungen nicht mehr erfüllt, können Sie einen zweiten Dienst in einem höheren Tarif erstellen, Ihre Indizes für den zweiten Dienst neu erstellen und dann den ersten Dienst löschen.

## <a name="next-steps"></a>Nächste Schritte

Möchten Sie Ihre Cloudausgaben optimieren und dabei sparen?

> [!div class="nextstepaction"]
> [Beginnen mit der Kostenanalyse mit Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

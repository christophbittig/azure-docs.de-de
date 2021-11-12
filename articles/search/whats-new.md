---
title: Neuerungen in der kognitiven Azure-Suche
description: Ankündigungen neuer und erweiterter Features, einschließlich der Umbenennung des Diensts von Azure Search in kognitive Azure-Suche.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 07/20/2021
ms.custom: references_regions
ms.openlocfilehash: a3554cee4d5538fc8291ef2a2db5c852c33b137a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503582"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Neuerungen in der kognitiven Azure-Suche

Informieren Sie sich über die Neuerungen im Dienst. Legen Sie ein Lesezeichen für diese Seite an, um über den Dienst auf dem Laufenden zu bleiben. In der [Liste der Previewfunktionen](search-api-preview.md) finden Sie eine umfassende Liste der Features, die noch nicht allgemein verfügbar sind.

## <a name="november-2021"></a>November 2021

|Funktion&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  BESCHREIBUNG | Verfügbarkeit  |
|------------------------------------|--------------|---------------|
| [Azure Files-Indexer (Vorschau)](./search-file-storage-integration.md) | Zusätzliche REST-API-Unterstützung zum Erstellen von Indexern für [Azure Files](https://azure.microsoft.com/services/storage/files/) | Public Preview |

## <a name="july-2021"></a>Juli 2021

|Funktion&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  BESCHREIBUNG | Verfügbarkeit  |
|------------------------------------|--------------|---------------|
| [Search-REST-API 2021-04-30-Preview](/rest/api/searchservice/index-preview) | Fügt REST-API-Unterstützung für Indexerverbindungen hinzu, die mit [verwalteten Identitäten](search-howto-managed-identities-data-sources.md) und Azure Active Directory-Authentifizierung (Azure AD) hergestellt werden. | Public Preview |
| [Rollenbasierte Autorisierung (Vorschau)](search-security-rbac.md) | Authentifizieren Sie sich mit Azure Active Directory und neuen integrierten Rollen für den Datenebenenzugriff auf Indizes und die Indizierung, wodurch die Abhängigkeit von API-Schlüsseln vermieden oder verringert wird. | Öffentliche Vorschau ([auf Anforderung](./search-security-rbac.md?tabs=config-svc-portal%2croles-portal%2ctest-portal#step-1-preview-sign-up)). Nachdem Ihr Abonnement integriert wurde, verwenden Sie das Azure-Portal oder die Verwaltungs-REST-API-Version 2021-04-01-Preview, um einen Suchdienst für die Authentifizierung auf Datenebene zu konfigurieren.|
| [Verwaltungs-REST-API 2021-04-01-Preview](/rest/api/searchmanagement/) | Ändert [Dienst erstellen oder aktualisieren,](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) um neue [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) zu unterstützen. | Public Preview |

## <a name="may-2021"></a>Mai 2021

|Funktion&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  BESCHREIBUNG | Verfügbarkeit  |
|------------------------------------|--------------|---------------|
| [Power Query-Connectorunterstützung (Vorschau)](search-how-to-index-power-query-data-sources.md) | Indexer können jetzt von anderen Cloudplattformen aus indizieren. Wenn Sie einen Indexer verwenden, um externe Datenquellen zur Indizierung zu crawlen, können Sie jetzt Power Query-Connectors verwenden, um eine Verbindung zu Amazon Redshift, Elasticsearch, PostgreSQL, Salesforce Objects, Salesforce Reports, Smartsheet und Snowflake herzustellen. </br></br>[Ankündigung (Techcommunity-Blog)](https://techcommunity.microsoft.com/t5/azure-ai/azure-cognitive-search-indexers-allow-you-to-ingest-data-from/ba-p/2381988)  | Öffentliche Vorschau ([auf Anforderung](https://aka.ms/azure-cognitive-search/indexer-preview)), unter Verwendung von REST api-version=2020-06-30-Preview und dem Azure-Portal. |
|[Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) | Die ADLS Gen2-Datenquelle, die von Indexern verwendet wird, ist jetzt allgemein verfügbar. | Allgemein verfügbar, unter Verwendung von REST api-version=2020-06-30-Preview und dem Azure-Portal. |
|[MySQL-Unterstützung (Vorschau)](search-howto-index-mysql.md) | Für indexerbasierte Indizierung, mit Ankündigung einer Vorschau der Datenquellenunterstützung für Azure MySQL. | Öffentliche Vorschau, REST api-version=2020-06-30-Preview, [.NET SDK 11.2.1](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype.mysql) und Azure-Portal. |
| [Weitere queryLanguage-Elemente für Rechtschreibprüfung und semantische Ergebnisse](/rest/api/searchservice/preview-api/search-documents#queryLanguage) | Für Abfrageanforderungen, die die Rechtschreibprüfung oder „queryType=semantic“ aufrufen, können Sie „queryLanguage“ jetzt auf eine nicht englische Sprache ([38 Sprachen](/rest/api/searchservice/preview-api/search-documents#queryLanguage)) festlegen. </br></br>[Ankündigung (Techcommunity-Blog)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-multilingual-support-for-semantic-search-on-azure/ba-p/2385110) | Öffentliche Vorschau ([auf Anforderung](https://aka.ms/SemanticSearchPreviewSignup)). </br></br>Verwenden Sie [Search Documents (REST)](/rest/api/searchservice/preview-api/search-documents) api-version=2020-06-30-Preview, [Azure.Search.Documents 11.3.0-beta.2](https://www.nuget.org/packages/Azure.Search.Documents/11.3.0-beta.2) oder den [Suchexplorer](search-explorer.md) im Azure-Portal. </br></br>Es gelten [Regions- und Tarifeinschränkungen](semantic-search-overview.md#availability-and-pricing). |
| [Verfügbarkeit doppelter Verschlüsselung](search-security-manage-encryption-keys.md#double-encryption) | Für Suchindizes und mit vom Kunden verwalteten Schlüsseln verschlüsselte Objekte wird nun die doppelte Verschlüsselung (Verschlüsselung von statischen und temporären Datenträgern) in allen unterstützten Regionen implementiert. | In allen Regionen, abhängig vom [Erstellungsdatum des Diensts](search-security-manage-encryption-keys.md#double-encryption). |

## <a name="april-2021"></a>April 2021

|Funktion&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  BESCHREIBUNG | Verfügbarkeit  |
|------------------------------|---------------|---------------|
| [Gremlin API-Unterstützung (Vorschau)](search-howto-index-cosmosdb-gremlin.md) | Für die indexerbasierte Indizierung können Sie jetzt eine Datenquelle erstellen, die Inhalte aus Cosmos DB abruft, auf die über die Gremlin-API zugegriffen wird. | Öffentliche Vorschau ([auf Anforderung](https://aka.ms/azure-cognitive-search/indexer-preview)) mit api-version=2020-06-30-Preview. |

## <a name="march-2021"></a>März 2021

|Funktion&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  BESCHREIBUNG | Verfügbarkeit  |
|------------------------------|---------------|---------------|
| [Semantische Suche (Vorschau)](semantic-search-overview.md) | Eine Sammlung von abfragebezogenen Features, die die Relevanz von Suchergebnissen mit sehr geringen Anpassungen einer Abfrageanforderung erheblich verbessern. </br></br>Die [semantische Rangfolge](semantic-ranking.md) berechnet Relevanzbewertungen mithilfe der semantischen Bedeutung von Wörtern und Inhalten. </br></br>[Semantische Beschriftungen](semantic-how-to-query-request.md) geben relevante Passagen aus dem Dokument zurück, die das Dokument am besten zusammenfassen. Die wichtigsten Begriffe oder Ausdrücke sind hervorgehoben. </br></br>[Semantische Antworten](semantic-answers.md) geben aus einem Suchdokument extrahierte Schlüsselpassagen zurück, die als direkte Antwort auf eine Abfrage formuliert werden, die wie eine Frage aussieht. | Öffentliche Vorschau ([auf Anforderung](https://aka.ms/SemanticSearchPreviewSignup)). </br></br>Verwenden Sie [Suchdokumente (REST)](/rest/api/searchservice/preview-api/search-documents) api-version=2020-06-30-Preview oder den [Suchexplorer](search-explorer.md) im Azure-Portal. </br></br>Es gelten Regions- und Tarifeinschränkungen. |
| [Rechtschreibprüfung für Abfragebegriffe (Vorschau)](speller-how-to-add.md) | Bevor die Abfragebegriffe die Suchmaschine erreichen, können sie auf Rechtschreibfehler überprüft werden. Die Option `speller` kann mit jedem Abfragetyp verwendet werden (einfach, vollständig oder semantisch). |  Öffentliche Vorschau, nur Rest, api-version=2020-06-30-Preview|
| [SharePoint Online-Indexer (Vorschau)](search-howto-index-sharepoint-online.md) | Dieser Indexer verbindet Sie mit einer SharePoint Online-Website, sodass Sie Inhalte aus einer Dokumentbibliothek indizieren können. | Öffentliche Vorschau, nur Rest, api-version=2020-06-30-Preview |
| [Normalisierungsfunktionen (Vorschau)](search-normalizers.md) | Normalisierungsfunktionen bieten einfache Textvorverarbeitung: konsistente Schreibweisen, Akzententfernung und ASCII-Faltung, ohne die Kette für die Volltextanalyse aufrufen zu müssen.| Öffentliche Vorschau, nur Rest, api-version=2020-06-30-Preview |
| [Skill für benutzerdefinierte Entitätssuche](cognitive-search-skill-custom-entity-lookup.md ) |  Eine kognitive Fähigkeit, die nach Text aus einer benutzerdefinierten Liste von Wörtern und Ausdrücken sucht. Mithilfe dieser Liste werden alle Dokumente mit übereinstimmenden Entitäten mit einer Bezeichnung markiert. Die Qualifikation unterstützt auch einen gewissen Grad an Fuzzyübereinstimmung, der für die Suche nach ähnlichen, aber nicht exakten Übereinstimmungen verwendet werden kann. | Allgemein verfügbar. |

## <a name="february-2021"></a>Februar 2021

|Funktion&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  BESCHREIBUNG | Verfügbarkeit  |
|------------------------------|---------------|---------------|
| [Zurücksetzen von Dokumenten (Vorschau)](search-howto-run-reset-indexers.md) |  Erneute Verarbeitung von einzeln ausgewählten Suchdokumenten in Indexerworkloads. | [Search-REST-API 2020-06-30-Preview](/rest/api/searchservice/index-preview) |
| [Verfügbarkeitszonen](search-performance-optimization.md#availability-zones)| Für Suchdienste mit zwei oder mehr Replikaten in bestimmten Regionen (siehe [Skalieren zur Verbesserung der Leistung in Azure Cognitive Search](search-performance-optimization.md#availability-zones)) wird Resilienz erzielt, indem Replikate an zwei oder mehr getrennten physischen Standorten vorgehalten werden.  | Die Verfügbarkeit richtet sich nach der Region und dem Datum der Erstellung des Suchdiensts. Ausführliche Informationen finden Sie im Artikel „Skalieren zur Verbesserung der Leistung“. |
| [Azure CLI](/cli/azure/search) </br>[Azure PowerShell](/powershell/module/az.search/) | Neue Revisionen decken jetzt das gesamte Spektrum von Vorgängen in der Verwaltungs-REST-API 2020-08-01 ab – einschließlich Unterstützung von IP-Firewallregeln und privatem Endpunkt. | Allgemein verfügbar. |

## <a name="january-2021"></a>Januar 2021

|Funktion&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  BESCHREIBUNG | Verfügbarkeit  |
|------------------------------|-------------|---------------|
| [Solution Accelerator für Azure Cognitive Search und QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Pullen von Fragen und Antworten aus dem Dokument sowie zum Vorschlagen der Antworten mit der höchsten Relevanz. Eine Livedemo-App finden Sie unter [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo).  | Open-Source-Projekt (keine SLA) |

## <a name="2020-archive"></a>2020: Archiv

| Month (Monat) | Funktion | BESCHREIBUNG |
|-------|---------|-------------|
| November | [Verschlüsselung mit kundenseitig verwalteten Schlüsseln (erweitert)](search-security-manage-encryption-keys.md) | Erweitert die kundenseitig verwaltete Verschlüsselung für alle Ressourcen, die von einem Suchdienst erstellt und verwaltet werden. Allgemein verfügbar.|
| September | [Visual Studio Code-Erweiterung für Azure Cognitive Search](search-get-started-vs-code.md) | Hinzufügung von Arbeitsbereich, Navigationselementen, IntelliSense und Vorlagen für die Erstellung von Indizes, Indexern, Datenquellen und Skillsets. Dieses Feature ist zurzeit als öffentliche Preview verfügbar.| 
| September | [Verwaltete Dienstidentität (Indexer)](search-howto-managed-identities-data-sources.md) | Allgemein verfügbar.  |
| September | [Ausgehende Anforderungen über eine Private Link-Instanz](search-indexer-howto-access-private.md) | Allgemein verfügbar.  |
| September | [Verwaltungs-REST-API (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | Allgemein verfügbar. |
| September | [Verwaltungs-REST-API (2020-08-01-Preview)](/rest/api/searchmanagement/management-api-versions) | Fügt eine gemeinsam genutzte Private Link-Ressource für Azure Functions und Azure SQL für MySQL-Datenbanken hinzu. |
| September | [Management .NET SDK 4.0](/dotnet/api/overview/azure/search/management) |  Azure SDK-Update für das Management SDK mit der REST-API-Zielversion 2020-08-01. Allgemein verfügbar.|
| August | [Doppelte Verschlüsselung](search-security-overview.md#encryption) | Allgemein verfügbar für alle Suchdienste, die nach dem 1. August 2020 in den folgenden Regionen erstellt wurden: USA, Westen 2; USA, Osten; USA, Süden-Mitte; US Gov Virginia; US Gov Arizona. |
| Juli | [Azure.Search.Documents-Clientbibliothek](/dotnet/api/overview/azure/search.documents-readme) | Azure SDK für .NET, allgemein verfügbar. |
| Juli | [azure.search.documents-Clientbibliothek](/python/api/overview/azure/search-documents-readme)  | Azure SDK für Python, allgemein verfügbar. |
| Juli | [@azure/search-documents-Clientbibliothek](/javascript/api/overview/azure/search-documents-readme)  | Azure SDK für JavaScript, allgemein verfügbar. |
| June | [Wissensspeicher](knowledge-store-concept-intro.md) | Allgemein verfügbar. |
| June | [REST-API 2020-06-30 suchen](/rest/api/searchservice/) | Allgemein verfügbar. |
| June | [Search-REST-API 2020-06-30-Preview](/rest/api/searchservice/) | Hinzufügung von „Skillset zurücksetzen“ für die selektive erneute Verarbeitung von Skills und inkrementelle Anreicherung. |
| June | [Okapi BM25-Relevanzalgorithmus](index-ranking-similarity.md) | Allgemein verfügbar. |
| June |  **executionEnvironment** (für Suchdienste mit Azure Private Link) | Allgemein verfügbar. |
| June | [AML-Skill (Vorschau)](cognitive-search-aml-skill.md) | Kognitiver Skill, um die KI-Anreicherung mit einem benutzerdefinierten AML-Modell (Azure Machine Learning) zu erweitern. |
| May | [Debugsitzungen (Vorschau)](cognitive-search-debug-session.md) | Skillset-Debugger im Portal.  |
| May | [IP-Regeln für eingehende Firewallunterstützung](service-configure-firewall.md) | Allgemein verfügbar.  |
| May | [Azure Private Link für einen privaten Suchendpunkt](service-create-private-endpoint.md) | Allgemein verfügbar.  |
| May | [Verwaltete Dienstidentität (Indexer) (Vorschau)](search-howto-managed-identities-data-sources.md) | Verbinden von Azure-Datenquellen mit einer verwalteten Identität.  |
| May | [sessionId-Abfrageparameter](index-similarity-and-scoring.md), [scoringStatistics=global parameter](index-similarity-and-scoring.md#scoring-statistics)  | Globale Suchstatistik, nützlich für [Machine Learning-Modelle (LearnToRank) für Suchrelevanz](https://github.com/Azure-Samples/search-ranking-tutorial).  |
| May | [Erweiterung der featuresMode-Relevanzbewertung (Vorschau)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|März  | [Natives vorläufiges Löschen von Blobs (Vorschau)](search-howto-index-changed-deleted-blobs.md) | Löschen von Suchdokumenten, wenn das Quellblob im Blobspeicher vorläufig gelöscht wird. |
|März  | [Verwaltungs-REST-API (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | Allgemein verfügbar. |
|Februar | [Qualifikation „PII-Erkennung“ (Vorschau)](cognitive-search-skill-pii-detection.md)  | Kognitiver Skill, mit dem persönliche Informationen extrahiert und maskiert werden. |
|Februar | [Qualifikation “Benutzerdefinierte Entitätssuche“ (Vorschau)](cognitive-search-skill-custom-entity-lookup.md) | Kognitiver Skill zum Finden von Wörtern und Ausdrücken in einer Liste und zum Bezeichnen aller Dokumente mit übereinstimmenden Entitäten.  |
|January | [Verschlüsselung mit kundenseitig verwalteten Schlüsseln](search-security-manage-encryption-keys.md) | Allgemein verfügbar  |
|January | [IP-Regeln für eingehende Firewallunterstützung(Vorschau)](service-configure-firewall.md) | Neue **IpRule**- und **NetworkRuleSet**-Eigenschaften in [CreateOrUpdate-API](/rest/api/searchmanagement/2020-08-01/services/create-or-update).  |
|January | [Erstellen eines privaten Endpunkts (Vorschau)](service-create-private-endpoint.md) | Einrichten einer Private Link-Instanz für sichere Verbindungen mit Ihrem Suchdienst. Im Rahmen der Lösung verfügt diese Previewfunktion über eine Abhängigkeit von [Azure Private Link](../private-link/private-link-overview.md) und [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). |

## <a name="2019-archive"></a>2019: Archiv

| Month (Monat) | Funktion | BESCHREIBUNG |
|-------|---------|-------------|
|Dezember | [Erstellen einer Demo-App](search-create-app-portal.md) | Ein Assistent, mit dem eine herunterladbare HTML-Datei mit (schreibgeschütztem) Abfragezugriff auf einen Index generiert wird. Dies ist als Überprüfungs- und Testtool konzipiert und soll nicht als Abkürzung zur Erstellung einer vollständigen Client-App dienen.|
|November | [Inkrementelle Anreicherung (Vorschau)](cognitive-search-incremental-indexing-conceptual.md) | Zwischenspeichern der Verarbeitung von Skillsets zur zukünftigen Verwendung.  |
|November | [Kognitiver Skill „Dokumentextrahierung“ (Vorschau)](cognitive-search-skill-document-extraction.md) | Kognitiver Skill zum Extrahieren des Inhalts einer Datei aus einem Skillset.|
|November | [Skill „Textübersetzungs-API“](cognitive-search-skill-text-translation.md) | Kognitiver Skill für die Nutzung während der Indizierung, mit dem Text evaluiert und übersetzt wird. Allgemein verfügbar.|
|November | [Power BI-Vorlagen](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | Vorlage zum Visualisieren von Inhalt im Wissensspeicher |
|November | [Azure Data Lake Storage Gen2 (Vorschauversion)](search-howto-index-azure-data-lake-storage.md) und [Cosmos DB-Gremlin-API (Vorschauversion)](search-howto-index-cosmosdb.md) | Neue Indexerdatenquellen, die sich in der öffentlichen Vorschauphase befinden. |
|Juli | [Unterstützung für die Azure Government-Cloud](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia&products=search) | Allgemein verfügbar.|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Neuer Dienstname

Azure Search wurde im Oktober 2019 in **Azure Cognitive Search** umbenannt, um die erweiterte (optionale) Verwendung von kognitiven Skills und KI-Verarbeitung bei wichtigen Vorgängen widerzuspiegeln. API-Versionen, NuGet-Pakete, Namespaces und Endpunkte bleiben unverändert. Neue und bereits vorhandene Suchlösungen sind von der Änderung des Dienstnamens nicht betroffen.

## <a name="service-updates"></a>Dienstupdates

[Ankündigungen von Dienstupdates](https://azure.microsoft.com/updates/?product=search&status=all) für die kognitive Azure-Suche finden Sie auf der Azure-Website.
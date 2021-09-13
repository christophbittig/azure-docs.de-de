---
title: Datenquellenkatalog
titleSuffix: Azure Cognitive Search
description: Dieser Artikel listet alle unterstützten Datenquellen für den Import in einen Azure Cognitive Search-Index auf.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
layout: LandingPage
ms.date: 06/23/2021
ms.openlocfilehash: e48c464ffa1aa5077f04ea1a7580e0d15e0fd7ca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346288"
---
# <a name="data-sources-gallery"></a>Datenquellenkatalog

Suchen Sie einen Datenconnector von Microsoft oder einem Partner, um die Datenerfassung in einem Suchindex zu vereinfachen. Dieser Artikel weist folgende Abschnitte auf:

+ [Allgemein verfügbare Datenquellen von Cognitive Search](#ga)
+ [Vorschaudatenquellen von Cognitive Search](#preview)
+ [Power Query-Connectors (Vorschau)](#powerquery)
+ [Datenquellen von unseren Partnern](#partners)

<a name="ga"></a>

## <a name="generally-available-data-sources-by-cognitive-search"></a>Allgemein verfügbare Datenquellen von Cognitive Search

Übernehmen Sie Inhalte aus anderen Azure-Diensten mithilfe von Indexern und den folgenden Datenquellenconnectors. 

:::row:::
:::column span="":::

---

### <a name="azure-blob-storage"></a>Azure Blob Storage

von [Cognitive Search](search-what-is-azure-search.md)

Extrahieren Sie Blobmetadaten und -inhalte, die in JSON-Dokumente serialisiert und als Suchdokumente in einen Suchindex importiert werden. Legen Sie Eigenschaften in Datenquellen- und Indexerdefinitionen fest, um eine Optimierung für verschiedene Blobinhaltstypen durchzuführen. Die Änderungserkennung wird automatisch unterstützt.

[Weitere Informationen](search-howto-indexing-azure-blob-storage.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL-API)

von [Cognitive Search](search-what-is-azure-search.md)

Stellen Sie über die SQL-API eine Verbindung zu Cosmos DB her, um Elemente aus einem Container zu extrahieren, die in JSON-Dokumente serialisiert und als Suchdokumente in einen Suchindex importiert werden. Konfigurieren Sie die Änderungsnachverfolgung, um den Suchindex mit den neuesten Änderungen in Ihrer Datenbank zu aktualisieren.

[Weitere Informationen](search-howto-index-cosmosdb.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-sql-database"></a>Azure SQL-Datenbank

von [Cognitive Search](search-what-is-azure-search.md)

Extrahieren Sie Feldwerte aus einer einzelnen Tabelle oder Sicht, die in JSON-Dokumente serialisiert und als Suchdokumente in einen Suchindex importiert werden. Konfigurieren Sie die Änderungsnachverfolgung, um den Suchindex mit den neuesten Änderungen in Ihrer Datenbank zu aktualisieren.

[Weitere Informationen](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

:::image type="icon" source="media/search-data-sources-gallery/azuresqlconnectorlogo_medium.png":::

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-table-storage"></a>Azure Table Storage

von [Cognitive Search](search-what-is-azure-search.md)

Extrahieren Sie Zeilen aus einer Azure-Tabelle, die in JSON-Dokumente serialisiert und als Suchdokumente in einen Suchindex importiert werden. 

[Weitere Informationen](search-howto-indexing-azure-tables.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

von [Cognitive Search](search-what-is-azure-search.md)

Stellen Sie über Azure Data Laker Storage Gen2 eine Verbindung zu Azure Storage her, um Inhalte aus einer Hierarchie von Verzeichnissen und geschachtelten Unterverzeichnissen zu extrahieren.

[Weitere Informationen](search-howto-index-azure-data-lake-storage.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::row-end::': null
':::row::': null
':::column span=""::':
  ':::column-end::': null
  ':::column span=""::': null
---

<a name="preview"></a>

## <a name="preview-data-sources-by-cognitive-search"></a>Vorschaudatenquellen von Cognitive Search

Neue Datenquellen werden als Previewfunktionen ausgegeben. [Melden Sie sich an](https://aka.ms/azure-cognitive-search/indexer-preview), um zu beginnen.

:::row:::
:::column span="":::

---

### <a name="cosmos-db-gremlin-api"></a>Cosmos DB (Gremlin-API)

von [Cognitive Search](search-what-is-azure-search.md)

Stellen Sie über die Gremlin-API eine Verbindung zu Cosmos DB her, um Elemente aus einem Container zu extrahieren, die in JSON-Dokumente serialisiert und als Suchdokumente in einen Suchindex importiert werden. Konfigurieren Sie die Änderungsnachverfolgung, um den Suchindex mit den neuesten Änderungen in Ihrer Datenbank zu aktualisieren.

[Weitere Informationen](search-howto-index-cosmosdb-gremlin.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::column span="":::

---

### <a name="cosmos-db-mongo-api"></a>Cosmos DB (Mongo-API)

von [Cognitive Search](search-what-is-azure-search.md)

Stellen Sie über die Mongo-API eine Verbindung zu Cosmos DB her, um Elemente aus einem Container zu extrahieren, die in JSON-Dokumente serialisiert und als Suchdokumente in einen Suchindex importiert werden. Konfigurieren Sie die Änderungsnachverfolgung, um den Suchindex mit den neuesten Änderungen in Ihrer Datenbank zu aktualisieren.

[Weitere Informationen](search-howto-index-cosmosdb.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

von [Cognitive Search](search-what-is-azure-search.md)

Stellen Sie eine Verbindung zu einer SharePoint Online-Site her, und indizieren Sie Dokumente aus einer oder mehreren Dokumentbibliotheken für Konten und Suchdienste im gleichen Mandanten. Text und normalisierte Bilder werden standardmäßig extrahiert. Optional können Sie ein Skillset für eine erweiterte Inhaltstransformation und -anreicherung konfigurieren oder die Änderungsnachverfolgung so konfigurieren, dass ein Suchindex mit neuen oder geänderten Inhalten in SharePoint aktualisiert wird.

[Weitere Informationen](search-howto-index-sharepoint-online.md)

:::image type="icon" source="media/search-data-sources-gallery/sharepoint_online_logo.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-mysql"></a>Azure MySQL

von [Cognitive Search](search-what-is-azure-search.md)

Stellen Sie eine Verbindung zur MySQL-Datenbank in Azure her, um Zeilen in einer Tabelle zu extrahieren, die in JSON-Dokumente serialisiert und als Suchdokumente in einen Suchindex importiert werden. Der Indexer nimmt bei nachfolgenden Ausführungen alle Änderungen, Uploads und Löschungen für Ihre MySQL-Datenbank vor und übernimmt diese Änderungen in Ihrem Suchindex.

[Weitere Informationen](search-howto-index-mysql.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_mysql.png":::

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::row-end::': null
':::row::': null
':::column span=""::':
  ':::column-end::': null
  ':::column span=""::': null
---

<a name="powerquery"></a>

## <a name="power-query-connectors-preview"></a>Power Query-Connectors (Vorschau)

Stellen Sie mithilfe von Indexern und einem Power Query-Connector als Datenquelle eine Verbindung zu Daten auf anderen Cloudplattformen her. [Melden Sie sich an](https://aka.ms/azure-cognitive-search/indexer-preview), um zu beginnen.

:::row:::
:::column span="":::

---

### <a name="amazon-redshift"></a>Amazon Redshift

Unterstützt von [Power Query](/power-query/power-query-what-is-power-query)

Stellen Sie eine Verbindung zu [Amazon Redshift](https://aws.amazon.com/redshift/) her, und extrahieren Sie durchsuchbaren Inhalt für die Indizierung in Cognitive Search.

[Weitere Informationen](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="elasticsearch"></a>Elasticsearch

Unterstützt von [Power Query](/power-query/power-query-what-is-power-query)

Stellen Sie eine Verbindung zu [Elasticsearch](https://www.elastic.co/elasticsearch) in der Cloud her, und extrahieren Sie durchsuchbaren Inhalt für die Indizierung in Cognitive Search.

[Weitere Informationen](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="postgresql"></a>PostgreSQL

Unterstützt von [Power Query](/power-query/power-query-what-is-power-query)

Stellen Sie eine Verbindung zu einer [PostgreSQL](https://www.postgresql.org/)-Datenbank in der Cloud her, und extrahieren Sie durchsuchbaren Inhalt für die Indizierung in Cognitive Search.

[Weitere Informationen](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="salesforce-objects"></a>Salesforce-Objekte

Unterstützt von [Power Query](/power-query/power-query-what-is-power-query)

Stellen Sie eine Verbindung zu Salesforce-Objekten her, und extrahieren Sie durchsuchbaren Inhalt für die Indizierung in Cognitive Search.

[Weitere Informationen](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="salesforce-reports"></a>Salesforce-Berichte

Unterstützt von [Power Query](/power-query/power-query-what-is-power-query)

Stellen Sie eine Verbindung zu Salesforce-Berichten her, und extrahieren Sie durchsuchbaren Inhalt für die Indizierung in Cognitive Search.

[Weitere Informationen](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="smartsheet"></a>Smartsheet

Unterstützt von [Power Query](/power-query/power-query-what-is-power-query)

Stellen Sie eine Verbindung zu Smartsheet her, und extrahieren Sie durchsuchbaren Inhalt für die Indizierung in Cognitive Search.

[Weitere Informationen](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="snowflake"></a>Snowflake

Unterstützt von [Power Query](/power-query/power-query-what-is-power-query)

Extrahieren Sie durchsuchbare Daten und Metadaten aus einer Snowflake-Datenbank, und füllen Sie einen Index basierend auf Feld-zu-Feld-Zuordnungen zwischen dem Index und Ihrer Datenquelle auf. 

[Weitere Informationen](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::column span=""::': null
---

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

<a name="partners"></a>

## <a name="data-sources-from-our-partners"></a>Datenquellen von unseren Partnern

Datenquellenconnectors werden auch von Microsoft-Drittanbietern bereitgestellt. Lesen Sie unsere [Nutzungsbedingungen](search-data-sources-terms-of-use.md), und lesen Sie die Anweisungen zur Partnerlizenzierung und -nutzung, bevor Sie eine Datenquelle verwenden.

:::row:::
:::column span="":::

---

### <a name="aderant"></a>Aderant

von [BA Insight](https://www.bainsight.com/)

Der Aderant-Connector sorgt für die Sicherheit des Quellsystems und bietet sowohl vollständige als auch inkrementelle Durchforstungen, sodass den Benutzern immer die neuesten Informationen zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/aderant-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

von [Accenture](https://www.accenture.com)

Ermöglicht Ihrem Unternehmen das Durchforsten von Inhalten vom Adobe Experience Manager-Server, um Verbindungsdrosselung und erwartete Werte oder Musterfilterung zu ermöglichen.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/AEM+Connector)

:::column-end:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

von [BA Insight](https://www.bainsight.com/)

Der Adobe Experience Manager-Connector ermöglicht die Indizierung von Inhalten, die von der AEM-Plattform (Adobe Experience Manager) verwaltet werden, und unterstützt sowohl vollständiges als auch inkrementelles Durchforsten, um die Aktualität des Index sicherzustellen.

[Weitere Informationen](https://www.bainsight.com/connectors/adobe-em-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Adobe Active Experience Manager (AEM) und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Seiten, Anlagen und weitere generierte Dokumenttypen von Adobe AEM werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig das Berechtigungsmodell von Adobe AEM, seine integrierte Benutzer- und Gruppenverwaltung sowie auf Active Directory oder anderen Verzeichnisdiensten basierende AEM-Installationen.

[Weitere Informationen](https://www.raytion.com/connectors/adobe-experience-manager-aem)

:::column-end:::
:::column span="":::

---

### <a name="alfresco"></a>Alfresco

von [BA Insight](https://www.bainsight.com/)

Der Alfresco-Connector basiert auf dem BAI-Connectorframework, bei dem es sich um die Plattform handelt, die zum Erstellen aller Connectors verwendet wird und eine sichere Konnektivität mit Unternehmenssystemen bietet.

[Weitere Informationen](https://www.bainsight.com/connectors/alfresco-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="alfresco"></a>Alfresco

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Alfresco One und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Dateien, Ordner und Benutzerprofile von Alfresco One werden nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig das Berechtigungsmodell von Alfresco One, seine integrierte Benutzer- und Gruppenverwaltung sowie auf Active Directory oder anderen Verzeichnisdiensten basierende Alfresco One-Installationen.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-alfresco-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="amazon-aurora"></a>Amazon Aurora

von [BA Insight](https://www.bainsight.com/)

Der Amazon Aurora-Connector basiert auf Datenbankzugriffsmethoden nach Industriestandard, sodass er Datenbanken aus anderen Systemen wie Oracle, MySQL und IBM DB2 gleichermaßen unterstützt.

[Weitere Informationen](https://www.bainsight.com/connectors/amazon-aurora-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="amazon-rds"></a>Amazon RDS

von [BA Insight](https://www.bainsight.com/)

Der Amazon RDS-Connector basiert auf Datenbankzugriffsmethoden nach Industriestandard, sodass er Datenbanken aus anderen Systemen wie Oracle, MySQL und IBM DB2 gleichermaßen unterstützen kann.

[Weitere Informationen](https://www.bainsight.com/connectors/amazon-rds-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="amazon-s3"></a>Amazon S3

von [BA Insight](https://www.bainsight.com/)

Der Amazon S3-Connector funktioniert mit allen in S3 gespeicherten Inhalten. Ihre Organisation kann den Connector verwenden, um eine sichere Verbindung mit S3 herzustellen und Inhalte aus S3-Buckets zu indizieren. Leistungsstarke Filterfunktionen geben Ihrer Organisation die Kontrolle darüber, welche in S3 gefundene Inhalte indiziert werden sollen.

[Weitere Informationen](https://www.bainsight.com/connectors/amazon-s3-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="aspider"></a>Aspider

von [Accenture](https://www.accenture.com)

Der Aspider-Connector ermöglicht u. a. das Durchforsten von Inhalten von Websites mithilfe von HTTP-Authentifizierung, inkrementellen Durchforstungen, Verbindungsdrosselung, verteilter und HTTPS-Durchforstung.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Aspider+Web+Crawler)

:::column-end:::
:::column span="":::

---

### <a name="atlassian-confluence-cloud"></a>Atlassian Confluence (Cloud)

von [BA Insight](https://www.bainsight.com/)

Unser Confluence-Connector (Cloudversion) ist ein Indizierungsconnector für Unternehmen, mit dem in Confluence gespeicherte Inhalte durchforstet und indiziert werden können.

[Weitere Informationen](https://www.bainsight.com/connectors/connector-for-confluence-cloud-version/)

:::column-end:::
:::column span="":::

---

### <a name="azure-ad"></a>Azure AD

von [BA Insight](https://www.bainsight.com)

Der BA Insight Azure Active Directory-Connector ermöglicht es, Inhalte aus Ihrem Azure Active Directory-Mandanten in einem einzelnen konsolidierten Suchindex zusammen mit Inhalten aus anderen Repositorys anzuzeigen, wodurch Suchvorgänge wie Mitarbeitersuche oder Expertensuche realisiert werden.

[Weitere Informationen](https://www.bainsight.com/connectors/azure-active-directory-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-ad"></a>Azure AD

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Microsoft Azure Active Directory (Azure AD) und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Er indiziert Objekte aus Azure AD über die Microsoft Graph-API. Der Connector kann für die Erfassung von Prinzipalen in Cognitive Search nahezu in Echtzeit verwendet werden, um Anwendungsfälle wie die Expertensuche, die Gerätesuche und die Standortsuche zu implementieren oder Early-Binding Security Trimming in Verbindung mit benutzerdefinierten Datenquellen zu ermöglichen. Der Connector unterstützt die Verbundauthentifizierung für Microsoft 365.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-azure-ad-connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-blobs"></a>Azure-Blobs

von [Accenture](https://www.accenture.com)

Bietet die Möglichkeit, Inhalte aus einem Azure-Blobcontainer zu durchforsten, wodurch inkrementelle Durchforstung, Sicherheit auf Dokumentebene und Zugriff auf Ordner und Unterordner ermöglicht wird.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Blob+Storage+Connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-data-lake"></a>Azure Data Lake

von [Accenture](https://www.accenture.com)

Der Azure Data Lake-Connector durchforstet Inhalte aus der Azure Data Lake Storage-Cloud entweder am Stamm oder an angegebenen Pfaden, mit inkrementellem Durchforsten, Abrufen von Objekt-ACLs, OAuth 2-Authentifizierung und mehr.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Data+Lake+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-events-hub"></a>Azure Events Hub

von [Accenture](https://www.accenture.com)

Durchforstet Inhalte aus einem Azure Events Hub, wodurch das inkrementelle Durchforsten und Abrufen beliebiger Ereignis- oder Attributtypen ermöglicht wird.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Events+Hub+Connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-sql-database"></a>Azure SQL-Datenbank

von [BA Insight](https://www.bainsight.com/)

Der Azure SQL-Datenbank-Connector von BA Insight sorgt für die Sicherheit der Quelldatenbank und bietet sowohl vollständige als auch inkrementelle Durchforstungen, damit Benutzern die neuesten Informationen immer zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/azure-sql-database-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="bentley"></a>Bentley

von [BA Insight](https://www.bainsight.com/)

Der BAI Bentley AssetWise-Connector ermöglicht es, Inhalte aus AssetWise zusammen mit Inhalten aus anderen Repositorys in einem einzigen konsolidierten Suchindex anzuzeigen.

[Weitere Informationen](https://www.bainsight.com/connectors/bentley-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="box"></a>Feld

von [Accenture](https://www.accenture.com)

Der Box-Connector durchforstet Inhalte aus einem Box-Repository. Der Connector ruft die unterstützten Elemente mithilfe der RESTful-API ab und bietet vollständiges oder inkrementelles Durchforsten, Metadatenextraktion, Abrufen von ACLs und mehr.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Box++Connector)

:::column-end:::
:::column span="":::

---

### <a name="box"></a>Feld

von [BA Insight](https://www.bainsight.com/)

Der Box-Connector ermöglicht es, Inhalte von Box in SharePoint und anderen Portalen anzuzeigen, sodass Benutzer integrierte Suchergebnisse von SharePoint und Box erhalten können.

[Weitere Informationen](https://www.bainsight.com/connectors/box-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="box"></a>Feld

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Box und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Dateien, Ordner, Kommentare, Benutzer, Gruppen und Aufgaben von Box werden nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von Box.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-box-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

von [Accenture](https://www.accenture.com)

Der Confluence-Connector durchforstet Inhalte aus allen Confluence-Inhaltsrepositorys. Der Connector ruft Spaces, Seiten, Blogs, Anlagen und Kommentare über eine REST-API ab und ermöglicht inkrementelle Durchforstung, Abrufen von ACLs, Unterstützung für HTTP und HTTPS und mehr.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Atlassian+Confluence+Connector)

:::column-end:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

von [BA Insight](https://www.bainsight.com/)

Der Confluence-Connector ist ein Indizierungsconnector für Unternehmen, mit dem in Confluence gespeicherte Inhalte durchforstet und indiziert werden können. Dadurch können SharePoint oder ein beliebiges anderes Portal als einzelner Punkt dienen, über den Benutzer den benötigten Inhalt aus mehreren Inhaltsquellen suchen und abrufen können.

[Weitere Informationen](https://www.bainsight.com/connectors/confluence-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Atlassian Confluence und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Seiten, Blogbeiträge, Anlagen, Kommentare, Spaces, Profile und Hubstandorte für Tags von lokalen Confluence-Instanzen werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von Atlassian Confluence sowie auf Active Directory oder anderen Verzeichnisdiensten basierende Confluence-Installationen.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-confluence-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="confluence-cloud"></a>Confluence Cloud

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Atlassian Confluence Cloud und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Seiten, Blogbeiträge, Anlagen, Kommentare, Spaces, Profile und Hubstandorte für Tags von Confluence Cloud-Instanzen werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von Atlassian Confluence Cloud.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-confluence-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="cuadrastar"></a>CuadraSTAR

von [BA Insight](https://www.bainsight.com/)

Der CuadraSTAR-Connector durchforstet Inhalte in CuadraSTAR und erstellt einen einzelnen Index, der es ermöglicht, mit Azure Cognitive Search relevante Informationen in CuadraSTAR und über 70 anderen unterstützten Repositorys zu finden, sodass keine separaten Suchvorgänge mehr durchgeführt werden müssen.

[Weitere Informationen](https://www.bainsight.com/connectors/cuadrastar-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="database"></a>Datenbank

von [Accenture](https://www.accenture.com)

Der Datenbankserver-Connector durchforstet Inhalte von einem relationalen Datenbankserver, scannt alle Datenbanken auf dem Server und extrahiert Zeilen- und Tabelleninformationen.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Database+Server+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="deltek"></a>Deltek

von [BA Insight](https://www.bainsight.com/)

Der Deltek Vision-Connector sorgt für die Sicherheit des Quellsystems und bietet sowohl vollständige als auch inkrementelle Durchforstungen, sodass den Benutzern immer die neuesten Informationen zur Verfügung stehen. Er indiziert Inhalte von Deltek Vision in Azure, SharePoint Online oder SharePoint 2016/2013 und zeigt sie über den SmartHub von BA Insight an, um Benutzern integrierte Suchergebnisse zu bieten.

[Weitere Informationen](https://www.bainsight.com/connectors/deltek-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

von [Accenture](https://www.accenture.com)

Der DQL-Connector für Aspire Documentum durchforstet Inhalte aus Documentum und ermöglicht Durchforstungen basierend auf benutzerdefinierten DQL-SELECT-Anweisungen, inkrementelle Durchforstung, Abrufen von ACLs, Gruppenerweiterung geschachtelter Berechtigungen und mehr.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Documentum+DQL+Connector)

:::column-end:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

von [BA Insight](https://www.bainsight.com/)

Der Documentum Connector von BA Insight indiziert sowohl den vollständigen Text als auch die Metadaten von Documentum-Objekten sicher in Azure Cognitive Search und ermöglicht so ein einzelnes durchsuchbares Resultset für Inhalte aus mehreren Repositorys. Dies unterscheidet sich von anderen Connectors, die Documentum-Datensätze mit Azure Cognitive Search nacheinander für die Prozessverwaltung verfügbar machen.

[Weitere Informationen](https://www.bainsight.com/connectors/documentum-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus OpenText Documentum und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Repositorys, Ordner und Dateien zusammen mit ihren Metadaten und Eigenschaften aus Documentum werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von OpenText Documentum.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-documentum-connector)

:::column-end:::
:::column span="":::

---

### <a name="egnyte"></a>egnyte

von [BA Insight](https://www.bainsight.com/)

Der Egnyte-Connector unterstützt sowohl vollständige als auch inkrementelle Durchforstungen und Indizes mit sehr hohem Durchsatz.

[Weitere Informationen](https://www.bainsight.com/connectors/egnyte-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="elasticsearch"></a>Elasticsearch

von [Accenture](https://www.accenture.com)

Der Elasticsearch-Connector durchforscht Inhalte aus einem Elasticsearch-Index und ermöglicht das Durchforsten mehrerer Indizes, Sliceunterstützung, die Verwendung von Get- oder MGet-Methoden zum Abrufen von Inhalten sowie die Verbindungsdrosselung.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Elasticsearch+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="elite--e3"></a>Elite/E3

von [BA Insight](https://www.bainsight.com/)

Der Elite-Connector von BA Insight bietet einen einzigen Zugriffspunkt für Anwälte, um mithilfe von Azure Cognitive Search auf Kanzleiinhalte und -wissen zuzugreifen, die mit Elite-Inhalten konform sind.

[Weitere Informationen](https://www.bainsight.com/connectors/elite-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="emc-eroom"></a>EMC eRoom

von [BA Insight](https://www.bainsight.com/)

Der eRoom-Connector stellt eine sichere Verbindung mit der eRoom-Anwendung her und ordnet den Inhalt, einschließlich Metadaten und Anlagen, aus dem eRoom-Schema dem Suchmaschinenschema zu. Anschließend werden Inhalte extrahiert und in einem Prozess namens Durchforstung an die Suchmaschine übertragen.

[Weitere Informationen](https://www.bainsight.com/connectors/eroom-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="facebook-workplace"></a>Facebook Workplace

von [BA Insight](https://www.bainsight.com/)

Organisationen, die Workplace by Facebook verwenden, können die Reichweite dieser Daten jetzt über den BA Insight Workplace by Facebook-Connector auf ihre vorhandenen Suchindizes ausdehnen.

[Weitere Informationen](https://www.bainsight.com/connectors/connector-for-workplace-by-facebook/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="facebook-workplace"></a>Facebook Workplace

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Facebook Workplace und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Projektgruppen, Konversationen und freigegebene Dokumente von Facebook Workplace werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von Facebook Workplace.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-facebook-workplace-connector)

:::column-end:::
:::column span="":::
---

### <a name="file-share"></a>Dateifreigabe

von [BA Insight](https://www.bainsight.com/)

Mit dem Dateifreigabeconnector können Inhalte aus Dateifreigaben (Windows, SMB/CIFS) zusammen mit Inhalten aus anderen Repositorys in einem einzigen konsolidierten Suchindex angezeigt werden.

[Weitere Informationen](https://www.bainsight.com/connectors/file-share-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="file-system"></a>Dateisystem

von [Accenture](https://www.accenture.com)

Der Dateisystemconnector durchforstet Inhalte von einem Dateisystemspeicherort, ermöglicht inkrementelle Durchforstung, Metadatenextraktion, Filterung von Dokumenten nach Pfad und unterstützt Windows-/Linux-/MacOS-Dateisysteme.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/File+System+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::
---

### <a name="file-system"></a>Dateisystem

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus lokal eingebundenen Dateisystemen und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Dateien und Ordner aus Dateisystemen werden von ihm nahezu in Echtzeit robust indiziert.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-file-system-connector)

:::column-end:::
:::column span="":::

---

### <a name="firstspirit"></a>FirstSpirit

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus e-Spirit FirstSpirit und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Seiten, Anlagen und weitere generierte Dokumenttypen von FirstSpirit werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer-, Gruppen- und Berechtigungsverwaltung von e-FirstSpirit sowie FirstSpirit-Installationen, die auf Active Directory und anderen Verzeichnisdiensten basieren.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-firstspirit-connector)

:::column-end:::
:::column span="":::

---

### <a name="gitlab"></a>GitLab

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus GitLab und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Projekte, Dateien, Ordner, Commitnachrichten, Probleme und Wiki-Seiten von GitLab werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von GitLab.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-gitlab-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="google-cloud-sql"></a>Google Cloud SQL

von [BA Insight](https://www.bainsight.com/)

Der Google Cloud SQL-Connector indiziert Inhalte von Google Cloud SQL in den Azure Cognitive Search-Index, der über den SmartHub von BA Insight angezeigt wird, um Benutzern integrierte Suchergebnisse zur Verfügung zu stellen.

[Weitere Informationen](https://www.bainsight.com/connectors/google-cloud-sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="google-drive"></a>Google Drive

von [BA Insight](https://www.bainsight.com/)

Der BAI Google Drive-Connector ermöglicht es, Inhalte von Google Drive in einem einzelnen konsolidierten Suchindex anzuzeigen, der auf Google Drive-Inhalte sowie auf Inhalte aus anderen Repositorys verweist.

[Weitere Informationen](https://www.bainsight.com/connectors/google-drive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="google-drive"></a>Google Drive

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Google Drive und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Dateien, Ordner und Kommentare auf persönlichen Laufwerken und Teamlaufwerken von Google Drive werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig das integrierte Berechtigungsmodell von Google Drive sowie die Benutzer- und Gruppenverwaltung durch das Google Admin Directory.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-google-drive-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="hp-consolidated-archive-eas"></a>HP Consolidated Archive (EAS)

von [BA Insight](https://www.bainsight.com/)

Der HP Consolidated Archive Connector von BA Insight indiziert den vollständigen Text und die Metadaten von Dokumenten in Archiven sicher in verschiedene Suchmaschinen, einschließlich SharePoint Search und Azure Search. Dies ermöglicht ein einzelnes durchsuchbares Resultset für Inhalte aus mehreren Repositorys. Dadurch können Organisationen die Vielzahl von Informationen nutzen, auf die im konsolidierten Archiv, in SharePoint und anderen Repositorys zugegriffen werden kann, wodurch diese Daten den Benutzern über die Suche sofort zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/hp-consolidated-archive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

von [Accenture](https://www.accenture.com)

Der IBM Connections-Connector durchforstet Inhalte vom IBM Connections-Server mit inkrementeller Durchforstung, Metadatenextraktion, Abrufen von ACLs, Filtern von Dokumenten nach RegEx-Mustern und mehr.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/IBM+Connections+Connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

von [BA Insight](https://www.bainsight.com/)

Der IBM Connections-Connector wurde für IBM Connections entwickelt, um eine sichere Verbindung mit der Connections-Anwendung herzustellen und den Inhalt, einschließlich Metadaten und Anlagen, vom Connections-Schema dem Suchmaschinenschema zuzuordnen. Anschließend werden Inhalte extrahiert und in einem Prozess namens Durchforstung an die Suchmaschine übertragen.

[Weitere Informationen](https://www.bainsight.com/connectors/ibm-connections-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus IBM Connections und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Öffentliche und persönliche Dateien, Blogs, Wikis, Foren, Communitys, Lesezeichen, Profile und Statusaktualisierungen von lokalen Verbindungsinstanzen werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von IBM Connections sowie auf Active Directory oder anderen Verzeichnisdiensten basierende Connections-Installationen.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-ibm-connections-connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections-cloud"></a>IBM Connections Cloud

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus IBM Connections Cloud und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Öffentliche und persönliche Dateien, Blogs, Wikis, Foren, Communitys, Profile und Statusaktualisierungen von Connections Cloud werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von IBM Connections Cloud.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-ibm-connections-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-content-manager"></a>IBM Content Manager

von [BA Insight](https://www.bainsight.com/)

Der IBM Content Manager-Connector sorgt für die Sicherheit der Quellanwendungen und bietet sowohl vollständige als auch inkrementelle Durchforstungen, sodass den Benutzern immer die neuesten Informationen zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/ibm-content-manager-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-db2"></a>IBM Db2

von [BA Insight](https://www.bainsight.com/)

Mit dem Db2-Connector können Organisationen auf die Fülle von Daten zugreifen, die in DB2-Datenbanken und -Anwendungen gespeichert sind, und diese Daten sofort über die Suche für Benutzer nutzbar machen.

[Weitere Informationen](https://www.bainsight.com/connectors/ibm-db2-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-filenet-p8"></a>IBM FileNet P8

von [BA Insight](https://www.bainsight.com/)

Der IBM FileNet Content Manager-Connector ermöglicht Benutzern von SharePoint und anderen Portalen die sichere Suche nach Inhalten, die in FileNet-Repositorys gespeichert sind. Der Zugriff auf Inhalte wird durch die in FileNet eingerichtete Sicherheit bestimmt. Dadurch wird sichergestellt, dass der Zugriff auf Ihre Inhalte über ein anderes Portal genauso sicher ist wie direkt in FileNet.

[Weitere Informationen](https://www.bainsight.com/connectors/ibm-filenet-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-lotus-notes"></a>IBM Lotus Notes

von [BA Insight](https://www.bainsight.com/)

Mit dem IBM Notes-E-Mail-Connector von BA Insight haben Benutzer die Möglichkeit, Lotus Notes-E-Mails direkt in SharePoint oder einem anderen Portal zu durchsuchen. Die in IBM Notes definierte Sicherheit wird automatisch in der Suchfunktion widergespiegelt, sodass Benutzern Suchergebnisse aus ihrem eigenen Postfach, öffentlichen Postfächern und anderen Postfächern angezeigt werden, für die ihnen Zugriff gewährt wurde.

[Weitere Informationen](https://www.bainsight.com/connectors/lotus-notes-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-websphere"></a>IBM WebSphere

von [BA Insight](https://www.bainsight.com/)

Der WebSphere-Connector von BA Insight indiziert sowohl den vollständigen Text als auch die Metadaten von WebSphere-Objekten sicher in die Suchmaschine von Microsoft und ermöglicht so ein einzelnes durchsuchbares Resultset für Inhalte aus mehreren Repositorys. Auf diese Weise können Organisationen die Vielzahl von Informationen nutzen, auf die auf Microsoft-Plattformen zugegriffen werden kann, und diese Daten können den Benutzern sofort über die Suche zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/ibm-websphere-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="imanage-cloud"></a>iManage Cloud

von [BA Insight](https://www.bainsight.com/)

Der iManage Cloud Connector von BA Insight indiziert den vollständigen Text und die Metadaten von Dokumenten in den Work-Arbeitsbereichen sicher in die Suchmaschine.

[Weitere Informationen](https://www.bainsight.com/connectors/connector-for-imanage-work-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="imanage-work"></a>iManage Work

von [BA Insight](https://www.bainsight.com/)

Der iManage Work-Connector bietet vollständige Sicherheit und arbeitet mit hohem Durchsatz, um Durchforstungszeiten zu minimieren und gleichzeitig eine geringe Auswirkung auf die Leistung von Work zu erhalten. Er erfordert nur Lesezugriff, und es ist nicht erforderlich, Clientsoftware auf einem iManage-Server zu installieren. Dies führt zu einem nahtlosen und gleichzeitigen Zugriff auf alle in iManage Work gespeicherten Inhalte.

[Weitere Informationen](https://www.bainsight.com/connectors/imanage-work-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="jira"></a>Jira

von [BA Insight](https://www.bainsight.com/)

Der Jira-Connector ermöglicht Benutzern Suchvorgänge für alle Jira-Objekte, sodass sie nicht direkt zu Jira wechseln müssen.

[Weitere Informationen](https://www.bainsight.com/connectors/jira-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="jira"></a>Jira

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Atlassian Jira und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Projekte, Probleme, Anlagen, Kommentare, Arbeitsprotokolle, Problemverläufe, Links und Profile aus lokalen Jira-Instanzen werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von Atlassian Jira sowie auf Active Directory oder anderen Verzeichnisdiensten basierende Jira-Installationen.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-jira-connector)

:::column-end:::
:::column span="":::

---

### <a name="jira-cloud"></a>Jira Cloud

von [BA Insight](https://www.bainsight.com/)

Der Jira-Connector (Cloudversion) führt Suchvorgänge für alle Jira-Objekte durch, sodass Sie nicht direkt zu Jira navigieren müssen.

[Weitere Informationen](https://www.bainsight.com/connectors/jira-cloud-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="jira-cloud"></a>Jira Cloud

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Atlassian Jira Cloud und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Projekte, Probleme, Anlagen, Kommentare, Arbeitsprotokolle, Problemverläufe, Links und Profile aus Jira Cloud werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von Atlassian Jira Cloud.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-jira-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="jive"></a>Jive

von [BA Insight](https://www.bainsight.com/)

Der Jive-Connector wurde für Jive entwickelt, um eine sichere Verbindung mit der Jive-Anwendung herzustellen und den Inhalt einschließlich Metadaten und Anlagen aus dem Jive-Schema dem Suchmaschinenschema zuzuordnen. Anschließend werden Inhalte extrahiert und in einem Prozess namens Durchforstung an die Suchmaschine übertragen.

[Weitere Informationen](https://www.bainsight.com/connectors/jive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="jive"></a>Jive

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Jive und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Diskussionen, Umfragen, Dateien, Blogs, Bereiche, Gruppen, Projekte, Aufgaben, Videos, Nachrichten, Ideen, Profile und Statusaktualisierungen von lokalen und in der Cloud gehosteten Jive-Instanzen werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von Jive und unterstützt die nativen Authentifizierungsmodelle von Jive, OAuth- und Standardauthentifizierung.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-jive-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="kaltura"></a>Kaltura

von [BA Insight](https://www.bainsight.com/)

Der Kaltura-Connector ermöglicht nicht nur die Indizierung von Videos, sondern auch verschiedener anderer Arten von Informationen, einschließlich Kategorien, Daten, Dokumente und mehr.

[Weitere Informationen](https://www.bainsight.com/connectors/kaltura-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ldap"></a>LDAP

von [BA Insight](https://www.bainsight.com/) 

Mit dem LDAP-Connector können Organisationen eine Verbindung mit jedem LDAP-kompatiblen Verzeichnis herstellen und jeden Datensatz daraus indizieren. Organisationen können nach bestimmten Teilmengen des Verzeichnisses filtern und nur bestimmte Felder abrufen. So ist es einfach, nach Benutzern, Kontakten oder Gruppen zu suchen, die an einem beliebigen Ort in Ihrem Verzeichnis gespeichert sind.

[Weitere Informationen](https://www.bainsight.com/connectors/ldap-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ldap"></a>LDAP

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Verzeichnisdiensten, die mit dem Lightweight Directory Access Protocol (LDAP) kompatibel sind, und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. LDAP-Objekte aus Active Directory, Novell E-Directory und anderen LDAP-kompatiblen Verzeichnisdiensten werden von ihm nahezu in Echtzeit robust indiziert. Der Connector kann für die Erfassung von Prinzipalen in Google Cloud Search für Anwendungsfälle wie Experten-, Geräte- und Standortsuchen oder zum Implementieren von Sicherheitsverkürzungen für benutzerdefinierte Datenquellen verwendet werden. Der Connector unterstützt LDAP über SSL.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-ldap-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="legalkey"></a>LegalKEY

von [BA Insight](https://www.bainsight.com/) 

Der OpenText LegalKEY-Connector von BA Insight indiziert sowohl den vollständigen Text als auch die Metadaten von Datensätzen von Mandanten und Rechtssachen in LegalKEY sicher in die Microsoft-Suchmaschine und ermöglicht so ein einzelnes durchsuchbares Resultset für Inhalte aus mehreren Repositorys. Dadurch können Organisationen die Vielzahl von Informationen nutzen, auf die in LegalKEY, in SharePoint und anderen Repositorys zugegriffen werden kann, wodurch diese Daten den Benutzern über die Suche sofort zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/legalkey-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="lexisnexis-interaction"></a>LexisNexis InterAction

von [BA Insight](https://www.bainsight.com/)

Der LexisNexis InterAction-Connector erleichtert es Anwälten und anderen Kanzleimitarbeitern in der gesamten Organisation, wichtige Informationen zu finden, die in InterAction gespeichert sind, ohne sich direkt anmelden und eine separate Suche durchführen zu müssen.

[Weitere Informationen](https://www.bainsight.com/connectors/lexisnexis-interaction-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="lotus-notes-databases"></a>Lotus Notes-Datenbanken

von [BA Insight](https://www.bainsight.com/) 

Mit dem IBM Notes Database Connector können Benutzer in Notes-Datenbanken gespeicherte Inhalte mithilfe von Azure Cognitive Search suchen. Die in IBM Notes definierte Sicherheit wird automatisch in der Suchfunktion widergespiegelt, wodurch sichergestellt wird, dass Benutzern Inhalte angezeigt werden, für die sie autorisiert sind. Letztendlich können Benutzer alles, was sie benötigen, an einem Ort finden.

[Weitere Informationen](https://www.bainsight.com/connectors/ibm-lotus-notes-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="m-files"></a>M-Files

von [BA Insight](https://www.bainsight.com/)

Der M-Files-Connector ermöglicht die Indizierung von Inhalten, die von der M-Files-Plattform verwaltet werden, und unterstützt sowohl vollständige als auch inkrementelle Durchforstung, um die Aktualität des Index sicherzustellen.

[Weitere Informationen](https://www.bainsight.com/connectors/m-files-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="mediaplatform-primetime"></a>MediaPlatform PrimeTime

von [BA Insight](https://www.bainsight.com/)

Der MediaPlatform PrimeTime-Indizierungsconnector von BA Insight ermöglicht es, den Inhalt für Benutzer über die Unternehmenssuche-Plattform einer Organisation zugänglich zu machen, indem der Connector mit dem SmartHub von BA Insight kombiniert wird. Der BA Insight MediaPlatform PrimeTime-Connector ruft Informationen zu Kanälen und Videos von MediaPlatform PrimeTime ab und indiziert sie über Azure Cognitive Search.

[Weitere Informationen](https://www.bainsight.com/connectors/mediaplatform-primetime-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="mediawiki"></a>MediaWiki

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus MediaWiki und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Seiten, Diskussionsseiten und Anlagen von MediaWiki-Instanzen werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig das integrierte Berechtigungsmodell von MediaWiki sowie MediaWiki-Installationen, die auf Active Directory und anderen Verzeichnisdiensten basieren.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-mediawiki-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="micro-focus-content-manager-hpe-records-managerhp-trim"></a>Micro Focus Content Manager (HPE Records Manager/HP TRIM)

von [BA Insight](https://www.bainsight.com/)

Der HP TRIM-Connector wurde für HP Records Manager entwickelt, um eine sichere Verbindung mit der TRIM-Anwendung herzustellen und den Inhalt einschließlich Metadaten und Anlagen aus dem TRIM-Schema dem Suchmaschinenschema zuzuordnen. Anschließend werden Inhalte extrahiert und in einem Prozess namens Durchforstung an die Suchmaschine übertragen.

[Weitere Informationen](https://www.bainsight.com/connectors/hp-trim-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-dynamics-365"></a>Microsoft Dynamics 365

von [BA Insight](https://www.bainsight.com/)

Unser Microsoft Dynamics 365 CRM-Connector unterstützt sowohl lokale CRM-Installationen als auch Dynamics CRM Online.

[Weitere Informationen](https://www.bainsight.com/connectors/microsoft-dynamics-crm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-dynamics-365-cloud"></a>Microsoft Dynamics 365 (Cloud)

von [BA Insight](https://www.bainsight.com/)

Unser Microsoft Dynamics 365 CRM-Connector (Cloudversion) stellt eine sichere Verbindung mit der CRM-Anwendung her und ordnet den Inhalt aus dem CRM-Schema dem Suchmaschinenschema zu.

[Weitere Informationen](https://www.bainsight.com/connectors/connector-for-microsoft-dynamics-cloud/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="microsoft-exchange-online"></a>Microsoft Exchange Online

von [BA Insight](https://www.bainsight.com/)

Mithilfe des BA Insight Microsoft Exchange Online-Connectors können Benutzer Inhalte aus Exchange Online über verschiedene Suchplattformen abrufen.

[Weitere Informationen](https://www.bainsight.com/connectors/microsoft-exchange-online-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-exchange-public-folders"></a>Microsoft Exchange Public Folders

von [BA Insight](https://www.bainsight.com/)

Mithilfe des BAI Microsoft Exchange Public Folders-Connectors können Benutzer Inhalte aus Exchange über verschiedene Suchplattformen abrufen.

[Weitere Informationen](https://www.bainsight.com/connectors/microsoft-exchange-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-exchange-server"></a>Microsoft Exchange Server

von [BA Insight](https://www.bainsight.com/)

Mithilfe des BA Insight Microsoft Exchange-Connectors können Benutzer Inhalte aus Exchange über verschiedene Suchmaschinen abrufen.

[Weitere Informationen](https://www.bainsight.com/connectors/microsoft-exchange-server-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

von [BA Insight](https://www.bainsight.com/)

Der Datenbankconnector basiert auf Datenbankzugriffsmethoden nach Industriestandard, sodass er Datenbanken aus anderen Systemen wie Oracle, MySQL und IBM DB2 gleichermaßen unterstützen kann. Dabei werden die Sicherheit der Quelldatenbank und sowohl vollständige als auch inkrementelle Durchforstungen unterstützt, sodass Benutzern immer die neuesten Informationen zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-teams"></a>Microsoft Teams

von [BA Insight](https://www.bainsight.com/)

Der BA Insight Microsoft Teams-Connector indiziert Inhalte aus Microsoft Teams neben Inhalten aus anderen Unternehmenssystemen, um einheitliche Ergebnisse zu liefern.

[Weitere Informationen](https://www.bainsight.com/connectors/microsoft-teams-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-windows-file-server"></a>Microsoft Windows-Dateiserver

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten von Microsoft Windows-Dateiserver, einschließlich des verteilten Dateisystems (DFS), und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Dateien und Ordner aus Windows-Dateiserver werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die Sicherheit auf Dokumentebene von Microsoft Windows-Dateiserver und die neuesten Versionen der Protokolle SMB2 und SMB3.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-windows-file-server-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="mysql"></a>MySQL

von [BA Insight](https://www.bainsight.com/)

Der MySQL-Connector basiert auf Datenbankzugriffsmethoden nach Industriestandard, sodass er Datenbanken aus anderen Systemen wie Oracle, MySQL und IBM DB2 gleichermaßen unterstützen kann. Dabei werden die Sicherheit der Quelldatenbank und sowohl vollständige als auch inkrementelle Durchforstungen unterstützt, sodass Benutzern immer die neuesten Informationen zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/mysql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="netdocuments"></a>NetDocuments

von [BA Insight](https://www.bainsight.com/)

Der NetDocuments-Connector indiziert in NetDocs gespeicherte Inhalte, sodass Benutzer NetDocuments-Inhalte direkt aus ihrem Portal suchen und abrufen können. Der Connector wendet die Dokumentsicherheit in NetDocs automatisch auf Azure Cognitive Search an, sodass Benutzerinformationen sicher bleiben. In NetDocuments gespeicherte Metadaten können gleichwertigen Begriffen zugeordnet werden, sodass Benutzer eine nahtlose Suchfunktion haben.

[Weitere Informationen](https://www.bainsight.com/connectors/netdocuments-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="neudesic-the-firm-directory"></a>Neudesic The Firm Directory

von [BA Insight](https://www.bainsight.com/)

Der The Firm Directory-Connector sorgt für die Sicherheit des Quellsystems und bietet sowohl vollständige als auch inkrementelle Durchforstungen, sodass den Benutzern immer die neuesten Informationen zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/the-firm-directory-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="notes"></a>Notizen

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus IBM Notes (früher Lotus Notes) und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Datensätze aus einer konfigurierbaren Gruppe von Notes-Datenbanken werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von IBM Notes.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-notes-connector)

:::column-end:::
:::column span="":::

---

### <a name="nuxeo"></a>Nuxeo

von [BA Insight](https://www.bainsight.com/)

Mit dem Nuxeo-Connector können Organisationen ihre Nuxeo-Inhalte, einschließlich Sicherheitsinformationen und Standard- und benutzerdefinierte Metadaten, die für Inhalte festgelegt wurden, zusammen mit Inhalten aus Office 365 in Azure Cognitive Search indizieren. Letztendlich können Benutzer alles, was sie benötigen, an einem Ort finden.

[Weitere Informationen](https://www.bainsight.com/connectors/nuxeo-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="objective"></a>Ziel

von [BA Insight](https://www.bainsight.com/)

Der Objective-Connector wurde für Objective entwickelt, stellt eine sichere Verbindung mit Objective her und ordnet den Inhalt einschließlich Metadaten aus dem Objective-Schema dem Suchmaschinenschema zu. Anschließend werden Inhalte extrahiert und in einem Prozess namens Durchforstung an die Suchmaschine übertragen.

[Weitere Informationen](https://www.bainsight.com/connectors/objective-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="onedrive"></a>OneDrive

von [Accenture](https://www.accenture.com)

Der OneDrive-Connector durchforstet Inhalte aus Microsoft OneDrive und ermöglicht inkrementelle Durchforstung unter Ein- und Ausschluss von auf RegEx-Mustern basierenden Elementen, Metadatenextraktion und Abrufen verschiedener Dokumenttypen.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/OneDrive+Connector)

:::column-end:::
:::column span="":::

---

### <a name="onedrive-for-business"></a>OneDrive for Business

von [BA Insight](https://www.bainsight.com/)

Der BA Insight OneDrive-Connector ermöglicht das Indizieren von Inhalten von OneDrive in verschiedene Suchplattformen, um Benutzern integrierte Suchergebnisse aus mehreren Quellen zu bieten.

[Weitere Informationen](https://www.bainsight.com/connectors/onedrive-business-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="opentext-content-server"></a>OpenText Content Server

von [BA Insight](https://www.bainsight.com/)

Der Connector indiziert Content Server-Inhalte ähnlich wie den nativen Portalinhalt und unterstützt sowohl vollständige Durchforstungen als auch inkrementelle Durchforstungen. Die in Content Server definierte Sicherheit wird automatisch in der Suchfunktion widergespiegelt, wodurch sichergestellt wird, dass Benutzern Inhalte angezeigt werden, für die sie autorisiert sind.

[Weitere Informationen](https://www.bainsight.com/connectors/livelink-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="opentext-content-server"></a>OpenText Content Server

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus OpenText Content Server und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Dateien, Ordner, virtuelle Ordner, Verbunddokumente, Nachrichten, E-Mails, Volumes, Sammlungen, Klassifizierungen und viele weitere Objekte aus Content Server-Instanzen werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von OpenText Content Server.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-opentext-content-server-connector)

:::column-end:::
:::column span="":::

---

### <a name="opentext-documentum-cloud"></a>OpenText Documentum (Cloud)

von [BA Insight](https://www.bainsight.com/)

Der OpenText Documentum-Cloudconnector von BA Insight indiziert sowohl den vollständigen Text als auch die Metadaten von Documentum-Objekten sicher in die Suchmaschine und ermöglicht so ein einzelnes durchsuchbares Resultset für Inhalte aus mehreren Repositorys.

[Weitere Informationen](https://www.bainsight.com/connectors/connector-for-documentum-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="opentext-documentum-eroom"></a>OpenText Documentum eRoom

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus OpenText Documentum eRoom und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Repositorys, Ordner und Dateien zusammen mit ihren Metadaten und Eigenschaften aus Documentum eRoom werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von OpenText Documentum eRoom.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-opentext-documentum-eroom-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="opentext-edocs-dm"></a>OpenText eDOCS DM

von [BA Insight](https://www.bainsight.com/)

Benutzer des OpenText eDOCS DM-Connectors können direkt innerhalb von Azure Cognitive Search nach Inhalten suchen, die in eDOCS-Repositorys gespeichert sind, sodass nicht mehrere Suchvorgänge erforderlich sind, um die benötigten Inhalte zu finden. Die in eDOCS eingerichtete Sicherheit wird vom Connector verwaltet, um sicherzustellen, dass Inhalte nur von Personen gesehen werden, denen Zugriff gewährt wurde.

[Weitere Informationen](https://www.bainsight.com/connectors/edocs-dm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="oracle-database"></a>Oracle-Datenbank

von [BA Insight](https://www.bainsight.com/)

Der Oracle Database-Connector basiert auf Datenbankzugriffsmethoden nach Industriestandard, sodass er Datenbanken aus anderen Systemen wie Microsoft SQL Server, MySQL und IBM DB2 gleichermaßen unterstützen kann.

[Weitere Informationen](https://www.bainsight.com/connectors/oracle-database-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="oracle-webcenter"></a>Oracle WebCenter

von [BA Insight](https://www.bainsight.com/)

Der WebCenter-Connector integriert WebCenter in Azure Cognitive Search, was es Benutzern in der gesamten Organisation erleichtert, wichtige Informationen zu finden, die in WebCenter gespeichert sind, ohne sich direkt anmelden und eine separate Suche durchführen zu müssen.

[Weitere Informationen](https://www.bainsight.com/connectors/oracle-webcenter-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="oracle-ka-cloud"></a>Oracle KA Cloud

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Oracle Knowledge Advanced (KA) Cloud und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Seiten und Anlagen aus Oracle KA Cloud werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von Oracle KA Cloud. Der Connector verarbeitet insbesondere ausschnittbasierte Berechtigungen auf Oracle KA Cloud-Seiten.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-oracle-ka-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="oracle-webcenter-content-ucmstellent"></a>Oracle WebCenter Content (UCM/Stellent)

von [BA Insight](https://www.bainsight.com/)

Der WebCenter Content-Connector unterstützt vollständig die zugrunde liegende Sicherheit aller Inhalte, die Azure Cognitive Search zur Verfügung gestellt werden, und hält diesen Inhalt über geplante Durchforstungen auf dem neuesten Stand, um sicherzustellen, dass Benutzer bei einer Suche die neuesten Updates erhalten.

[Weitere Informationen](https://www.bainsight.com/connectors/oracle-webcenter-content-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="pirobase-cms"></a>pirobase CMS

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus pirobase CMS und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Seiten, Anlagen und weitere generierte Dokumenttypen von pirobase CMS werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von pirobase CMS.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-pirobase-cms-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="postgresql"></a>PostgreSQL

von [BA Insight](https://www.bainsight.com/)

Der PostgreSQL-Connector von BA Insight sorgt für die Sicherheit der Quelldatenbank und bietet vollständige und inkrementelle Durchforstungen, sodass Benutzern immer die neuesten Informationen zur Verfügung stehen. Er indiziert Inhalte aus PostgreSQL in Azure Cognitive Search und stellt sie über den SmartHub von BA Insight zur Verfügung, um Benutzern integrierte Suchergebnisse zu liefern.

[Weitere Informationen](https://www.bainsight.com/connectors/postgresql-connector-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="practical-law"></a>Practical Law

von [BA Insight](https://www.bainsight.com/)

Der BA Insight Practical Law-Connector ermöglicht es Benutzern, Suchvorgänge für die Practical Law-Datenbank durchzuführen, sodass sie nicht direkt zu Practical Law navigieren müssen.

[Weitere Informationen](https://www.bainsight.com/connectors/practical-law-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="prolaw"></a>ProLaw

von [BA Insight](https://www.bainsight.com/)

Der BA Insight-Connector für ProLaw verbindet jedes Portal mit ProLaw, sodass Informationen von ProLaw angezeigt werden können, während die Benutzerberechtigungen in ProLaw beachtet werden.

[Weitere Informationen](https://www.bainsight.com/connectors/prolaw-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="rdb-via-snapshots"></a>RDB via Snapshots

von [Accenture](https://www.accenture.com)

Der RDB via Snapshots-Connector durchforscht Inhalte aus jeder relationalen Datenbank, auf die über JDBC zugegriffen werden kann, und führt inkrementelle Durchforstungen mithilfe einer Momentaufnahmedatenbank durch. Er extrahiert Daten direkt auf Grundlage von SQL-Anweisungen.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/RDB+via+Snapshots+Connector)

:::column-end:::
:::column span="":::

---

### <a name="rdb-via-tables"></a>RDB via Tables

von [Accenture](https://www.accenture.com)

Der RDB via Tables-Connector durchforscht Inhalte aus jeder relationalen Datenbank, auf die mithilfe von JDBC zugegriffen werden kann, und führt inkrementelle Durchforstungen durch, indem Updates mithilfe von Tabellen abgerufen werden, die Bezeichner des aktualisierten Inhalts enthalten. Er extrahiert Daten direkt auf Grundlage von SQL-Anweisungen.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/RDB+via+Table+Connector)

:::column-end:::
:::column span="":::

---

### <a name="s3"></a>S3

von [Accenture](https://www.accenture.com)

Der Amazon S3-Connector durchforstet Inhalte von Amazon Simple Storage Service. Er führt inkrementelle Durchforstungen durch, ruft Objekt-ACLs für die Sicherheit auf S3-Dokumentebene ab und enthält Dokumente, die in Buckets, Ordnern und Unterordnern gespeichert sind.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Amazon+S3+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

von [Accenture](https://www.accenture.com)

Der Salesforce-Connector durchforstet Inhalte aus einem Salesforce-Repository. Der Connector unterstützt Salesforce Knowledge Base und Chatter, Metadaten- und benutzerdefinierte Metadatenabrufe, führt vollständige oder inkrementelle Durchforstungen durch, ruft ACLs, auswählbare Elementtypen und Gruppenerweiterungen ab.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Salesforce+Connector)

:::column-end:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

von [BA Insight](https://www.bainsight.com/)

Der Salesforce-Connector integriert die Service-, Sales- und Marketing-Cloud von Salesforce in Azure Cognitive Search und macht alle Inhalte in Salesforce über dieses Portal für alle Mitarbeiter verfügbar.

[Weitere Informationen](https://www.bainsight.com/connectors/salesforce-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Salesforce und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Konten, Chatter-Nachrichten, Profile, Leads, Fälle und alle anderen Datensatzobjekte aus Salesforce werden nahezu in Echtzeit von ihm robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von Salesforce.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-salesforce-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sap-erp"></a>SAP ERP

von [BA Insight](https://www.bainsight.com/)

Der SAP ERP-Connector von BA Insight ist darauf ausgelegt, Elemente aus SAP in einen Suchindex zu bringen. Dadurch wird wiederum die Benutzeroberfläche angezeigt und eine einheitliche Ansicht der Informationen in SAP, SharePoint und anderen Systemen ermöglicht.

[Weitere Informationen](https://www.bainsight.com/connectors/sap-erp-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="sap-erp-cloud"></a>SAP ERP (Cloud)

von [BA Insight](https://www.bainsight.com/)

Der SAP ERP-Connector von BA Insight (Cloudversion) ist darauf ausgelegt, Elemente aus SAP in einen Suchindex zu bringen.

[Weitere Informationen](https://www.bainsight.com/connectors/connector-for-sap-erp-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="sap-hana"></a>SAP HANA

von [BA Insight](https://www.bainsight.com/)

Der SAP HANA-Connector unterstützt die Sicherheit der Quelldatenbank und sowohl vollständige als auch inkrementelle Durchforstungen, sodass Benutzern immer die neuesten Informationen zur Verfügung stehen. Er indiziert Inhalte aus SAP HANA in Azure Cognitive Search und stellt sie über den SmartHub von BA Insight zur Verfügung, um Benutzern integrierte Suchergebnisse zu liefern.

[Weitere Informationen](https://www.bainsight.com/connectors/sap-hana-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sap-hana-cloud"></a>SAP HANA (Cloud)

von [BA Insight](https://www.bainsight.com/)

Der SAP HANA-Connector (Cloudversion) sorgt für die Sicherheit der Quelldatenbank und bietet sowohl vollständige als auch inkrementelle Durchforstungen, damit Benutzern immer die neuesten Informationen zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/connector-sap-hana-cloud-version/)

:::column-end:::
:::column span="":::

---

### <a name="sap-netweaver-portal"></a>SAP NetWeaver Portal

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus SAP NetWeaver Portal (NWP) und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Seiten, Anlagen und andere Dokumenttypen aus SAP NWP, den Bereichen „Knowledge Management and Collaboration“ (KMC) und „Portal Content Directory“ (PCD) werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von SAP NetWeaver Portal sowie auf Active Directory oder anderen Verzeichnisdiensten basierende SAP NWP-Installationen.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-sap-netweaver-portal-connector)

:::column-end:::
:::column span="":::

---

### <a name="sap-plm-dms"></a>SAP PLM DMS

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus SAP PLM DMS und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Dokumente, Anlagen und andere Datensätze aus SAP PLM DMS werden von ihm nahezu in Echtzeit robust indiziert.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-sap-plm-dms-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="selenium"></a>Selenium

von [Accenture](https://www.accenture.com)

Der Selenium-Connector durchforstet Inhalte von Websites mithilfe eines Internetbrowsers, um verschiedene Arten von Dokumenten wie Webseiten, Sitemaps, binäre Dokumente und mehr abzurufen. Er vermeidet Kompatibilitätsprobleme mit Frameworks wie Angular und React.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Selenium+Crawler)

:::column-end:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

von [Accenture](https://www.accenture.com)

Der ServiceNow-Connector ruft verschiedene Arten von Dokumenten aus einem ServiceNow-Repository ab, z. B. Wissensartikel, Katalogelemente und Anlagen. Außerdem ruft er Sicherheits-ACLs ab und führt eine Gruppenerweiterung aus.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/ServiceNow+Connector)

:::column-end:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

von [BA Insight](https://www.bainsight.com/)

 Der ServiceNow-Connector sorgt für die Sicherheit des Quellsystems und bietet sowohl vollständige als auch inkrementelle Durchforstungen, sodass den Benutzern immer die neuesten Informationen zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/servicenow-connector-sharepoint-azure-elasticsearch)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus ServiceNow und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Probleme, Aufgaben, Anlagen, Artikel zur Wissensverwaltung, Seiten, u. a. von ServiceNow werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt die integrierte Benutzer- und Gruppenverwaltung von ServiceNow.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-servicenow-connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint"></a>SharePoint

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Microsoft SharePoint und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Websites, Webs, moderne Seiten (SharePoint 2016 und höher) und klassische Seiten, Wiki-Seiten, OneNote-Dokumente, Listenelemente, Aufgaben, Kalenderelemente, Anlagen und Dateien von lokalen SharePoint-Instanzen werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von Microsoft SharePoint sowie Active Directory und OAuth-Anbieter wie SiteMinder und Okta. Der Connector bietet Unterstützung für die Standard-, NTLM- und Kerberos-Authentifizierung.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-sharepoint-connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2010"></a>SharePoint 2010

von [BA Insight](https://www.bainsight.com/)

Mit dem SharePoint 2010-Connector von BA Insight können Sie eine Verbindung mit SharePoint 2010 herstellen, Daten von einer beliebigen Website, Dokumentbibliothek oder Liste abrufen und diesen Inhalt sicher indizieren.

[Weitere Informationen](https://www.bainsight.com/connectors/sharepoint-2010-connector/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-2013"></a>SharePoint 2013

von [Accenture](https://www.accenture.com)

Der SharePoint 2013-Connector durchforstet Inhalte aus einer SharePoint 2013-Websitesammlungs-URL. Er führt inkrementelle Durchforstungen mithilfe des Änderungsprotokollzeitstempels von SharePoint oder einer Momentaufnahmedatenbank durch, ruft ACLs ab, unterstützt NTLM und HTTPS, externe BCS-Listen und wird ausgeführt, ohne dass etwas auf SharePoint installiert wird.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+2013+Connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2013"></a>SharePoint 2013

von [BA Insight](https://www.bainsight.com/)

Mit dem SharePoint 2013-Connector von BA Insight können Sie eine Verbindung mit SharePoint 2013 herstellen, Daten von einer beliebigen Website, Dokumentbibliothek oder Liste abrufen und diesen Inhalt sicher indizieren.

[Weitere Informationen](https://www.bainsight.com/connectors/sharepoint-2013-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2016"></a>SharePoint 2016

von [Accenture](https://www.accenture.com)

Der SharePoint 2016-Connector durchforstet Inhalte aus einer SharePoint 2016-Websitesammlungs-URL. Er führt inkrementelle Durchforstungen mithilfe des Änderungsprotokollzeitstempels von SharePoint oder einer Momentaufnahmedatenbank durch, ruft ACLs ab, unterstützt NTLM und HTTPS, externe BCS-Listen und wird ausgeführt, ohne dass etwas auf SharePoint installiert wird.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+2016+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-2016"></a>SharePoint 2016

von [BA Insight](https://www.bainsight.com/)

Mit dem SharePoint-Connector von BA Insight können Sie eine Verbindung mit SharePoint 2016 herstellen, Daten von einer beliebigen Website, Dokumentbibliothek oder Liste abrufen und diesen Inhalt sicher indizieren.

[Weitere Informationen](https://www.bainsight.com/connectors/sharepoint-2016-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2019"></a>SharePoint 2019

von [BA Insight](https://www.bainsight.com/)

Mit dem SharePoint-Connector von BA Insight können Sie eine Verbindung mit SharePoint 2019 herstellen, Daten von einer beliebigen Website, Dokumentbibliothek oder Liste abrufen und diesen Inhalt sicher indizieren.

[Weitere Informationen](https://www.bainsight.com/connectors/connector-for-sharepoint-2019/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

von [Accenture](https://www.accenture.com)

Der SharePoint Online-Connector durchforstet Inhalte aus einer SharePoint Online-Websitesammlungs-URL. Der Connector ruft Sites, Listen, Ordner, Listenelemente und Anlagen sowie andere Seiten (im ASPX-Format) ab. Unterstützt SharePoint, das im Microsoft O365-Angebot ausgeführt wird.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+Online+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

von [BA Insight](https://www.bainsight.com/)

Mit dem SharePoint Online-Connector von BA Insight können Sie eine Verbindung mit SharePoint Online herstellen, Daten von einer beliebigen Website, Dokumentbibliothek oder Liste abrufen und diesen Inhalt sicher indizieren.

[Weitere Informationen](https://www.bainsight.com/connectors/sharepoint-online-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sitecore"></a>Sitecore

von [BA Insight](https://www.bainsight.com/)

Der Sitecore-Connector sorgt für die Sicherheit des Quellsystems und bietet sowohl vollständige als auch inkrementelle Durchforstungen, sodass den Benutzern immer die neuesten Informationen zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/sitecore-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="sitecore"></a>Sitecore

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Sitecore und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Seiten, Anlagen und weitere generierte Dokumenttypen werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig das Berechtigungsmodell von Sitecore sowie die Benutzer- und Gruppenverwaltung im zugeordneten Active Directory.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-sitecore-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="slack"></a>Slack

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Slack und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Nachrichten, Threads und freigegebene Dateien aus allen öffentlichen Kanälen von Slack werden von ihm nahezu in Echtzeit zuverlässig indiziert.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-slack-connector)

:::column-end:::
:::column span="":::

---

### <a name="smb"></a>SMB

von [Accenture](https://www.accenture.com)

Der SMB-Connector ruft Dateien und Verzeichnisse auf freigegebenen Laufwerken ab. Er unterstützt verteilte Dateisysteme und das Abrufen von Sicherheitsinformationen und kann zur Indizierung auf Dokumente zugreifen, ohne das Datum des letzten Zugriffs zu ändern.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/SMB+Connector)

:::column-end:::
:::column span="":::

---

### <a name="smb-file-share"></a>SMB-Dateifreigabe

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus SMB-Dateifreigaben und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Dateien und Ordner aus Dateifreigaben werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die Sicherheit auf Dokumentebene von SMB und die neuesten Versionen der Protokolle SMB2 und SMB3.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-smb-file-share-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sql-database"></a>SQL-Datenbank

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus SQL-Datenbanken, z. B. Microsoft SQL Server oder Oracle, und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Datensätze und Felder, einschließlich binärer Dokumente aus SQL-Datenbanken, werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt die Implementierung eines benutzerdefinierten Sicherheitsmodells auf Dokumentebene.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-sql-database-connector)

:::column-end:::
:::column span="":::

---

### <a name="any-sql-based-crm-system"></a>Beliebiges SQL-basiertes CRM-System

von [BA Insight](https://www.bainsight.com/)

Der SQL Server-Connector basiert auf Datenbankzugriffsmethoden nach Industriestandard, sodass er Datenbanken aus anderen Systemen wie Oracle, MySQL und IBM DB2 gleichermaßen unterstützen kann. Dabei werden die Sicherheit der Quelldatenbank und sowohl vollständige als auch inkrementelle Durchforstungen unterstützt, sodass Benutzern immer die neuesten Informationen zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="symantec-enterprise-vault"></a>Symantec Enterprise Vault

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Symantec Enterprise Vault und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Archivierte Daten wie E-Mails, Anlagen, Dateien, Kalenderelemente und Kontakte aus Enterprise Vault werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die Standard-, NTLM- und Kerberos-Authentifizierungsmodelle von Symantec Enterprise Vault.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-enterprise-vault-connector-2)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="twitter"></a>Twitter

von [Accenture](https://www.accenture.com)

Der Twitter-Connector durchforstet Inhalte aus jedem Twitter-Konto. Er führt vollständige und inkrementelle Durchforstungen durch und unterstützt die Authentifizierung mit Twitter-Benutzer, Consumerschlüssel und geheimem Consumerschlüssel.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Twitter+Connector)

:::column-end:::
:::column span="":::

---

### <a name="veeva-vault"></a>Veeva Vault

von [BA Insight](https://www.bainsight.com/)

Der Veeva Vault-Connector von BA Insight indiziert sowohl den Volltext als auch die Metadaten von Veeva Vault-Objekten sicher in Azure Cognitive Search. Dadurch können Benutzer ein einzelnes Resultset für Inhalte in Veeva Vault und Microsoft 365 abrufen.

[Weitere Informationen](https://www.bainsight.com/connectors/veeva-vault-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="veritas-enterprise-vault-symantec-evault"></a>Veritas Enterprise Vault (Symantec eVault)

von [BA Insight](https://www.bainsight.com/)

Der Veritas Enterprise Vault-Connector sorgt für die Sicherheit des Quellsystems und bietet vollständige und inkrementelle Durchforstungen, sodass den Benutzern immer die neuesten Informationen zur Verfügung stehen.

[Weitere Informationen](https://www.bainsight.com/connectors/enterprise-vault-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="veritas-enterprise-vault"></a>Veritas Enterprise Vault

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Veritas Enterprise Vault und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Archivierte Daten wie E-Mails, Anlagen, Dateien, Kalenderelemente und Kontakte aus Enterprise Vault werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die Standard-, NTLM- und Kerberos-Authentifizierungsmodelle von Veritas Enterprise Vault.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-enterprise-vault-connector)

:::column-end:::
:::column span="":::
---

### <a name="website-crawler"></a>Website Crawler

von [BA Insight](https://www.bainsight.com/)

Mit dem Website Crawler-Connector von BA Insight können Inhalte aus Websites zusammen mit Inhalten aus anderen Repositorys in einem einzigen konsolidierten Suchindex angezeigt werden.

[Weitere Informationen](https://www.bainsight.com/connectors/website-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="west-km"></a>West km

von [BA Insight](https://www.bainsight.com/)

Der West km-Connector von BA Insight unterstützt die Suche über Geschäfts- und Prozessunterlagen hinweg, einschließlich der Erstellung benutzerdefinierter Ergebnisseiten.

[Weitere Informationen](https://www.bainsight.com/connectors/westkm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="windream-ecm-system"></a>windream ECM-System

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus windream ECM-System und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Dateien und Ordner, einschließlich der umfassenden, durch windream ECM-System zugeordneten Metadatensätze, werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig das Berechtigungsmodell von windream ECM-System sowie die Benutzer- und Gruppenverwaltung im zugeordneten Active Directory.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-windream-ecm-system-connector)

:::column-end:::
:::column span="":::

---

### <a name="xerox-docushare"></a>Xerox DocuShare

von [BA Insight](https://www.bainsight.com/)

Sie können direkt innerhalb von Azure Cognitive Search nach Inhalten suchen, die in DocuShare-Repositorys gespeichert sind, sodass nicht mehrere Suchvorgänge erforderlich sind, um die benötigten Inhalte zu finden.

[Weitere Informationen](https://www.bainsight.com/connectors/docushare-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::
---

### <a name="xerox-docushare"></a>Xerox DocuShare

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Xerox DocuShare und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Datenrepositorys, Ordner, Profile, Gruppen und Dateien von DocuShare werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von Xerox DocuShare.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-xerox-docushare-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

von [Accenture](https://www.accenture.com)

Der Yammer-Connector durchforstet Inhalte aus Yammer-Nachrichten. Er ruft Nachrichten nach Gruppe, Thread oder Thema ab und ruft Benutzer-, Gruppen- und Threaddetails ab.

[Weitere Informationen](https://contentanalytics.digital.accenture.com/display/aspire40/Yammer+Connector)

:::column-end:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

von [BA Insight](https://www.bainsight.com/)

Der Yammer-Connector stellt eine sichere Verbindung mit der Yammer-Anwendung her und ordnet den Inhalt, einschließlich Metadaten und Anlagen, aus dem Yammer-Schema dem Suchmaschinenschema zu. Anschließend werden Inhalte extrahiert und in einem Prozess namens Durchforstung an die Suchmaschine übertragen.

[Weitere Informationen](https://www.bainsight.com/connectors/yammer-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

von [Raytion](https://www.raytion.com/contact)

Sicherer Unternehmenssuche-Connector für die zuverlässige Indizierung von Inhalten aus Yammer und das intelligente Durchsuchen der Inhalte mit Azure Cognitive Search. Kanäle, Beiträge, Antworten, Anlagen, Umfragen und Ankündigungen von Yammer werden von ihm nahezu in Echtzeit robust indiziert. Der Connector unterstützt vollständig die integrierte Benutzer- und Gruppenverwaltung von Microsoft Yammer und insbesondere die Verbundauthentifizierung für Microsoft 365.

[Weitere Informationen](https://www.raytion.com/connectors/raytion-yammer-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---
':::column-end::': null
':::column span=""::': null
---

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

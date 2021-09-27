---
title: Anleitung zur Indexer-Problembehandlung
titleSuffix: Azure Cognitive Search
description: Dieser Artikel enthält Anleitungen zu Indexer-Problemen und -Lösungen für Fälle, in denen keine Fehlermeldungen von der Dienstsuche zurückgegeben werden.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2021
ms.openlocfilehash: 650f5f40bf8b8fc0909b4fec85ef6b5724a2e3c7
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539822"
---
# <a name="indexer-troubleshooting-guidance-for-azure-cognitive-search"></a>Leitfaden zur Indexer-Problembehandlung bei Azure Cognitive Search

Gelegentlich treten bei Indexern Probleme auf, und es gibt keine unterstützende Fehlerdiagnose. In diesem Artikel werden Probleme und mögliche Lösungen in Verbindung mit unerwarteten Indexer-Ergebnissen behandelt, zu denen nur begrenzte Informationen verfügbar sind. Wenn Sie einen Fehler untersuchen müssen, nutzen Sie stattdessen [Beheben von häufigen Fehler und Warnungen bei Suchindexern in Azure Cognitive Search](cognitive-search-common-errors-warnings.md).

## <a name="connection-errors"></a>Verbindungsfehler

> [!NOTE]
> Indexer haben eingeschränkten Zugriff auf Datenquellen und andere Ressourcen, die durch Azure-Netzwerksicherheitsmechanismen geschützt werden. Indexer können derzeit nur über entsprechende Einschränkungsmechanismen für den IP-Adressbereich oder gegebenenfalls über NSG-Regeln auf Datenquellen zugreifen. Details zum Zugriff auf die einzelnen unterstützten Datenquellen finden Sie weiter unten.
>
> Sie können die IP-Adresse Ihres Suchdiensts ermitteln, indem Sie seinen vollqualifizierten Domänennamen pingen (z. B. `<your-search-service-name>.search.windows.net`).
>
> Sie können den IP-Adressbereich des [Diensttags](../virtual-network/service-tags-overview.md#available-service-tags) `AzureCognitiveSearch` ermitteln, indem Sie entweder [herunterladbare JSON-Dateien](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) oder die [Diensttagermittlungs-API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) verwenden. Der IP-Adressbereich wird wöchentlich aktualisiert.

### <a name="firewall-rules"></a>Firewallregeln

Azure Storage, Cosmos DB und Azure SQL bieten eine konfigurierbare Firewall. Sie erhalten keine spezifische Fehlermeldung, wenn die Firewall aktiviert ist. Firewallfehler sind normalerweise generisch und sehen wie folgt aus: `The remote server returned an error: (403) Forbidden` oder `Credentials provided in the connection string are invalid or have expired`.

Es gibt zwei Möglichkeiten, Indexern in einem solchen Fall Zugriff auf diese Ressourcen zu ermöglichen:

* Deaktivieren Sie die Firewall, indem Sie den Zugriff über **alle Netzwerke** zulassen (falls möglich).

* Alternativ können Sie den Zugriff für die IP-Adresse des Suchdiensts und den IP-Adressbereich des `AzureCognitiveSearch`-[Diensttags](../virtual-network/service-tags-overview.md#available-service-tags) in den Firewallregeln der Ressource zulassen (Einschränkung des IP-Adressbereichs).

Details zum Konfigurieren der Einschränkungen des IP-Adressbereichs für die einzelnen Datenquellentypen finden Sie unter den folgenden Links:

* [Azure Storage (in englischer Sprache)](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Einschränkung**: Wie in der Dokumentation zu Azure Storage erläutert, funktionieren Einschränkungen des IP-Adressbereichs nur, wenn sich der Suchdienst und das Speicherkonto in unterschiedlichen Regionen befinden.

In Azure Functions (als [Skill für benutzerdefinierte Web-API](cognitive-search-custom-skill-web-api.md) verwendbar), werden auch [Einschränkungen für IP-Adressen](../azure-functions/ip-addresses.md#ip-address-restrictions) unterstützt. Die Liste der zu konfigurierenden IP-Adressen umfasst die IP-Adresse des Suchdiensts und den IP-Adressbereich des `AzureCognitiveSearch`-Diensttags.

Weitere Informationen zum Herstellen einer Verbindung mit einem virtuellen Computer finden Sie unter [Konfigurieren einer Verbindung eines Indexers der kognitiven Azure-Suche mit SQL Server auf einer Azure-VM](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

### <a name="configure-network-security-group-nsg-rules"></a>Konfigurieren von Regeln für Netzwerksicherheitsgruppen (NSG)

Beim Zugriff auf Daten in einer verwalteten SQL-Instanz oder bei Verwendung eines virtuellen Azure-Computers als Webdienst-URI für einen [Skill für benutzerdefinierte Web-API](cognitive-search-custom-skill-web-api.md) müssen sich Kunden nicht um die spezifischen IP-Adressen kümmern.

In diesen Fällen können der virtuelle Azure-Computer oder die verwaltete SQL-Instanz so konfiguriert werden, dass sie sich in einem virtuellen Netzwerk befinden. Dann kann eine Netzwerksicherheitsgruppe konfiguriert werden, um die Art des eingehenden und ausgehenden Netzwerkdatenverkehrs von Subnetzen virtueller Netzwerke und Netzwerkschnittstellen zu filtern.

Das `AzureCognitiveSearch`-Diensttag kann direkt in den [NSG-Regeln](../virtual-network/manage-network-security-group.md#work-with-security-rules) für eingehenden Datenverkehr verwendet werden, ohne den zugehörigen IP-Adressbereich zu suchen.

Weitere Informationen zum Zugreifen auf Daten in einer verwalteten SQL-Instanz finden Sie [hier](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md).

## <a name="azure-sql-database-serverless-indexing-error-code-40613"></a>Azure SQL-Datenbank – serverlos: Indizierung (Fehlercode 40613)

Wenn sich Ihre SQL-Datenbank auf einer [serverlosen Computeebene](../azure-sql/database/serverless-tier-overview.md) befindet, müssen Sie sicherstellen, dass die Datenbank ausgeführt wird (und nicht angehalten wurde), wenn der Indexer eine Verbindung mit ihr herstellt.

Wenn die Datenbank angehalten wurde, wird sie bei der ersten Anmeldung Ihres Suchdiensts automatisch fortgesetzt. Darüber hinaus wird jedoch ein Fehler mit dem Fehlercode 40613 und dem Hinweis zurückgegeben, dass die Datenbank nicht verfügbar ist. Wenn die Datenbank ausgeführt wird, wiederholen Sie den Anmeldeversuch, um eine Verbindung herzustellen.

## <a name="sharepoint-online-conditional-access-policies"></a>Richtlinien für bedingten SharePoint Online-Zugriff

Beim Erstellen eines SharePoint Online-Indexers durchlaufen Sie einen Schritt, bei dem Sie sich nach dem Angeben eines Gerätecodes bei Ihrer Azure AD-App anmelden müssen. Möglicherweise erhalten Sie die Meldung `"Your sign-in was successful but your admin requires the device requesting access to be managed"`. In diesem Fall wird der Indexer wahrscheinlich durch eine Richtlinie für [bedingten Zugriff](../active-directory/conditional-access/overview.md) daran gehindert, auf die SharePoint Online-Dokumentbibliothek zuzugreifen.

Führen Sie die folgenden Schritte aus, um die Richtlinie so zu aktualisieren, dass der Indexer auf die Dokumentbibliothek zugreifen kann:

1. Öffnen Sie das Azure-Portal, suchen Sie nach **Bedingter Azure AD-Zugriff**, und klicken Sie dann im Menü links auf **Richtlinien**. Wenn Sie keinen Zugriff zur Anzeige dieser Seite haben, bitten Sie entweder einen Benutzer mit entsprechendem Zugriff, die Seite zu öffnen, oder fordern Sie Zugriff auf die Seite an.

1. Ermitteln Sie, welche Richtlinie den Zugriff des SharePoint Online-Indexers auf die Dokumentbibliothek blockiert. Die Richtlinie, die den Indexer möglicherweise blockiert, enthält das Benutzerkonto, das Sie während des Schritts zur Indexererstellung im Abschnitt **Benutzer und Gruppe** für die Authentifizierung verwendet haben. Die Richtlinie umfasst darüber hinaus möglicherweise **Bedingungen**, für die Folgendes gilt:
    * Einschränkung von **Windows**-Plattformen
    * Einschränkung von **Mobile Apps und Desktopclients**
    * **Gerätestatus** auf **Ja** festgelegt

1. Nachdem Sie bestätigt haben, dass eine Richtlinie den Indexer blockiert, müssen Sie als Nächstes eine Ausnahme für den Indexer erstellen. Rufen Sie die IP-Adresse des Suchdiensts ab.

    1. Rufen Sie den vollqualifizierten Domänennamen (FQDN) Ihres Suchdiensts ab. Dieser sieht wie `<search-service-name>.search.windows.net` aus. Sie können den FQDN ermitteln, indem Sie Ihren Suchdienst im Azure-Portal suchen.

   ![Abrufen des Dienst-FQDN](media\search-indexer-howto-secure-access\search-service-portal.png "Abrufen des Dienst-FQDN")

    Die IP-Adresse des Suchdiensts kann durch Ausführen von `nslookup` (oder von `ping`) des FQDN abgerufen werden. Im folgenden Beispiel fügen Sie einer eingehenden Regel in der Azure Storage-Firewall „150.0.0.1“ hinzu. Es kann bis zu 15 Minuten nach dem Aktualisieren der Firewalleinstellungen dauern, bis der Suchdienstindexer auf das Azure Storage-Konto zugreifen kann.

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. Rufen Sie die IP-Adressbereiche für die Indexerausführungsumgebung für Ihre Region ab.

    Zusätzliche IP-Adressen werden für Anforderungen verwendet, die aus der [mehrinstanzenfähigen Ausführungsumgebung](search-indexer-securing-resources.md#indexer-execution-environment) des Indexers stammen. Sie können diesen IP-Adressbereich aus dem Diensttag abrufen.

    Die IP-Adressbereiche für das Diensttag `AzureCognitiveSearch` können entweder über die [Ermittlungs-API (Vorschau)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) oder die [herunterladbare JSON-Datei](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) abgerufen werden.

    Für diese exemplarische Vorgehensweise sollte unter der Annahme, dass der Suchdienst der öffentliche Azure-Clouddienst ist, die [öffentliche Azure-JSON-Datei](https://www.microsoft.com/download/details.aspx?id=56519) heruntergeladen werden.

   ![Herunterladen der JSON-Datei](media\search-indexer-troubleshooting\service-tag.png "Herunterladen der JSON-Datei")

    Aus der JSON-Datei sind unter der Annahme, dass sich der Suchdienst in „USA, Westen-Mitte“ befindet, die IP-Adressen für die Ausführungsumgebung des Indexers für mehrere Mandanten nachfolgend aufgeführt.

    ```json
        {
          "name": "AzureCognitiveSearch.WestCentralUS",
          "id": "AzureCognitiveSearch.WestCentralUS",
          "properties": {
            "changeNumber": 1,
            "region": "westcentralus",
            "platform": "Azure",
            "systemService": "AzureCognitiveSearch",
            "addressPrefixes": [
              "52.150.139.0/26",
              "52.253.133.74/32"
            ]
          }
        }
    ```

1. Wählen Sie im Azure-Portal zurück auf der Seite „Bedingter Zugriff“ im Menü links die Option **Benannte Standorte** und anschließend **+ IP-Adressbereiche (Standort)** aus. Geben Sie dem neuen benannten Standort einen Namen, und fügen Sie die IP-Adressbereiche für Ihre Suchdienst- und Indexerausführungsumgebungen hinzu, die Sie in den letzten beiden Schritten erfasst haben.
    * Für die IP-Adresse Ihres Suchdiensts müssen Sie möglicherweise am Ende der IP-Adresse „/32“ hinzufügen, da nur gültige IP-Adressbereiche akzeptiert werden.
    * Denken Sie daran, dass Sie für die Indexerausführungsumgebung nur die IP-Adressbereiche der Region hinzufügen müssen, in der sich Ihr Suchdienst befindet.

1. Schließen Sie den neuen benannten Speicherort aus der Richtlinie aus. 
    1. Wählen Sie im Menü links **Richtlinien** aus. 
    1. Wählen Sie die Richtlinie aus, die den Indexer blockiert.
    1. Klicken Sie auf **Bedingungen**.
    1. Wählen Sie **Standorte** aus.
    1. Klicken Sie auf **Ausschließen**, und fügen Sie dann den neuen benannten Speicherort hinzu.
    1. **Speichern** Sie die Änderungen.

1. Warten Sie einige Minuten, bis die Richtlinie aktualisiert wurde, und erzwingen Sie die neuen Richtlinienregeln.

1. Versuchen Sie erneut, den Indexer zu erstellen.
    1. Senden Sie eine Aktualisierungsanforderung für das Datenquellenobjekt, das Sie erstellt haben.
    1. Senden Sie die Anforderung zum Erstellen des Indexers erneut. Verwenden Sie den neuen Code für die Anmeldung, und senden Sie eine weitere Anforderung zur Indexererstellung.

## <a name="indexing-unsupported-document-types"></a>Indizieren nicht unterstützter Dokumenttypen

Wenn Sie Inhalte aus Azure Blob Storage indizieren und der Container Blobs eines [nicht unterstützten Inhaltstyps](search-howto-indexing-azure-blob-storage.md#SupportedFormats) enthält, überspringt der Indexer dieses Dokument. In anderen Fällen können Probleme mit einzelnen Dokumenten auftreten. 

Sie können [Konfigurationsoptionen festlegen](search-howto-indexing-azure-blob-storage.md#DealingWithErrors), damit die Indexerverarbeitung bei Problemen mit einzelnen Dokumenten fortgesetzt werden kann.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

## <a name="missing-documents"></a>Fehlende Dokumente

Indexer extrahieren Dokumente oder Zeilen aus einer externen [Datenquelle](/rest/api/searchservice/create-data-source) und erstellen *Suchdokumente*, die dann vom Suchdienst indiziert werden. Gelegentlich wird ein Dokument, das in der Datenquelle vorhanden ist, nicht in einem Suchindex angezeigt. Dieses unerwartete Ergebnis kann aus folgenden Gründen auftreten:

* Das Dokument wurde nach der Ausführung des Indexers aktualisiert. Wenn Sie für Ihren Indexer einen [Zeitplan](/rest/api/searchservice/create-indexer#indexer-schedule) festgelegt haben, wird er das Dokument schließlich erneut ausführen und abrufen.
* Für den Indexer wurde vor der Erfassung des Dokuments ein Timeout ausgelöst. Es gibt [Grenzwerte für die maximale Bearbeitungszeit](search-limits-quotas-capacity.md#indexer-limits), nach deren Ablauf keine Dokumente mehr bearbeitet werden können. Sie können den Indexerstatus im Portal oder durch Aufrufen von [Get Indexer Status (REST API)](/rest/api/searchservice/get-indexer-status) überprüfen.
* [Feldzuordnungen](/rest/api/searchservice/create-indexer#fieldmappings) oder [KI-Anreicherung](./cognitive-search-concept-intro.md) haben das Dokument verändert, und seine Formulierung im Suchindex ist anders, als Sie es erwarten.
* [Änderungsnachverfolgungs-Werte](/rest/api/searchservice/create-data-source#data-change-detection-policies) sind fehlerhaft oder Voraussetzungen fehlen. Wenn der hohe Grenzwert ein Datum ist, das auf einen späteren Zeitpunkt festgelegt ist, werden alle Dokumente mit einem früheren Datum vom Indexer übersprungen. Sie können den Zustand der Änderungsnachverfolgung des Indexers mithilfe der Felder „initialTrackingState“ und „finalTrackingState“ im [Indexerstatus](/rest/api/searchservice/get-indexer-status#indexer-execution-result) ermitteln. Indexer für Azure SQL und MySQL müssen über einen Index für die Hochwassermarken-Spalte in der Quelltabelle verfügen. Andernfalls kann bei Abfragen des Indexers ein Timeout auftreten. 

> [!TIP]
> Wenn Dokumente fehlen, überprüfen Sie die von Ihnen verwendete [Abfrage](/rest/api/searchservice/search-documents), um sicherzustellen, dass sie die betreffenden Dokumente nicht ausschließt. Verwenden Sie für die Abfrage nach einem bestimmten Dokument die [Lookup Document-REST-API](/rest/api/searchservice/lookup-document).

## <a name="missing-content-from-blob-storage"></a>Fehlender Inhalt aus Blob Storage

Der Blobindexer [ findet und extrahiert Text aus Blobs in einem Container](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Beim Extrahieren von Text können u.a. diese Probleme auftreten:

* Das Dokument enthält nur gescannte Bilder. PDF-Blobs mit Nichttextinhalten wie gescannten Bildern (JPGs) erzeugen keine Ergebnisse in einer Standard-Blobindizierungspipeline. Wenn Sie Bildinhalte mit Textelementen haben, können Sie mithilfe der [kognitiven Suche](cognitive-search-concept-image-scenarios.md) Text finden und extrahieren.

* Der Blobindexer ist so konfiguriert, dass er nur Metadaten indiziert. Zum Extrahieren von Inhalten muss der Blobindexer so konfiguriert sein, dass er [sowohl Inhalte als auch Metadaten extrahiert](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex):

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="missing-content-from-cosmos-db"></a>Fehlender Inhalt aus Cosmos DB

Die kognitive Azure-Suche steht in impliziter Abhängigkeit zur Indizierung von Cosmos DB. Wenn Sie die automatische Indizierung in Cosmos DB deaktivieren, gibt die kognitive Azure-Suche einen erfolgreichen Status zurück, kann jedoch keine Containerinhalte indizieren. Anweisungen zum Überprüfen der Einstellungen und zum Aktivieren der Indizierung finden Sie unter [Verwalten der Indizierung in Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

## <a name="see-also"></a>Weitere Informationen

* [Problembehandlung bei häufigen Indexerfehlern und -warnungen](cognitive-search-common-errors-warnings.md)
* [Überwachen der indexerbasierten Indizierung](search-howto-monitor-indexers.md)
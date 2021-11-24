---
title: Indizieren von Daten über SharePoint Online (Vorschau)
titleSuffix: Azure Cognitive Search
description: Richten Sie einen SharePoint Online-Indexer ein, um die Indizierung der Inhalte von Dokumentbibliotheken in Azure Cognitive Search zu automatisieren.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: f0e5afcc4f0869c4fe12a634e34e7b0669da5c75
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137790"
---
# <a name="index-data-from-sharepoint-online"></a>Indizieren von Daten über SharePoint Online

> [!IMPORTANT] 
> Die Unterstützung für SharePoint Online befindet sich derzeit in der öffentlichen Vorschauphase unter [Zusätzliche Nutzungsbedingungen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Fordern Sie [Zugriff](https://aka.ms/azure-cognitive-search/indexer-preview) auf dieses Feature an, und verwenden Sie, nachdem der Zugriff aktiviert wurde, eine [Vorschau-REST-API (2020-06-30-preview oder höher)](search-api-preview.md), um Ihre Inhalte zu indizieren. Die Portalunterstützung ist momentan eingeschränkt, und das .NET SDK wird nicht unterstützt.

In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure Cognitive Search in SharePoint Online-Dokumentbibliotheken gespeicherte Dokumente (z. B. PDF- oder Microsoft Office-Dokumente und verschiedene andere gängige Formate) in einem Azure Cognitive Search-Index indizieren. Zunächst werden die Grundlagen der Einrichtung und Konfiguration des Indexers erläutert. Anschließend folgt eine ausführlichere Betrachtung der Verhaltensweisen und Szenarien, die Ihnen voraussichtlich begegnen.

> [!NOTE]
> SharePoint Online unterstützt ein detailliertes Autorisierungsmodell, das den benutzerspezifischen Zugriff auf Dokumentebene bestimmt. Der SharePoint Online-Indexer übernimmt diese Berechtigungen nicht in den Suchindex, und Cognitive Search unterstützt keine Autorisierung auf Dokumentebene. Wenn ein Dokument von SharePoint Online in einen Suchdienst indiziert wird, steht der Inhalt allen Benutzern zur Verfügung, die über Lesezugriff auf den Index verfügen. Wenn Sie Berechtigungen auf Dokumentebene benötigen, sollten Sie Sicherheitsfilter zum Einschränken von Ergebnissen nicht autorisierter Inhalte prüfen. Weitere Informationen finden Sie unter [Sicherheitsfilter zum Einschränken von Ergebnissen mit Active Directory-Identitäten](search-security-trimming-for-azure-search-with-aad.md).

## <a name="functionality"></a>Funktionalität

Ein Indexer in Azure Cognitive Search ist ein Crawler, der durchsuchbare Daten und Metadaten aus einer Datenquelle extrahiert. Der SharePoint Online-Indexer stellt eine Verbindung mit Ihrer SharePoint Online-Website her und indiziert Dokumente aus einer oder mehreren Dokumentbibliotheken. Der Indexer bietet die folgenden Funktionen:
+ Es können Inhalte aus einer oder mehreren SharePoint Online-Dokumentbibliotheken indiziert werden.
+ Der Indexer unterstützt die inkrementelle Indizierung, d. h. er ermittelt die Inhalte in der Dokumentbibliothek, die geändert wurden, und indiziert bei zukünftigen Indizierungsläufen nur die aktualisierten Inhalte. Wenn beispielsweise ursprünglich fünf PDF-Dateien vom Indexer indiziert wurden, indiziert er bei der erneuten Ausführung nur die eine PDF-Datei, die aktualisiert wurde.
+ Text und normalisierte Bilder werden standardmäßig aus den indizierten Dokumenten extrahiert. Optional kann der Pipeline ein Skillset zur weiteren Inhaltsanreicherung hinzugefügt werden. Weitere Informationen zu Skillsets finden Sie im Artikel [Skillsetkonzepte in Azure Cognitive Search](cognitive-search-working-with-skillsets.md).

## <a name="supported-document-formats"></a>Unterstützte Dokumentformate

Der SharePoint Online-Indexer für Azure Cognitive Search kann Text aus den folgenden Dokumentformaten extrahieren:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>Inkrementelle Indizierung und Erkennung von Löschungen
Der SharePoint Online-Indexer unterstützt standardmäßig die inkrementelle Indizierung, d. h. er ermittelt die Inhalte in der Dokumentbibliothek, die geändert wurden, und indiziert bei zukünftigen Indizierungsläufen nur die aktualisierten Inhalte. Wenn beispielsweise ursprünglich fünf Word-Dokumente vom Indexer indiziert wurden, indiziert er bei der nächsten Ausführung nur das eine Word-Dokument erneut, das aktualisiert wurde.

Die Erkennung von Löschungen wird ebenfalls standardmäßig unterstützt. Wenn ein Dokument aus einer SharePoint Online-Dokumentbibliothek gelöscht wird, erkennt der Indexer die Löschung bei einer zukünftigen Ausführung und entfernt das Dokument aus dem Index.

## <a name="setting-up-sharepoint-online-indexing"></a>Einrichten der SharePoint Online-Indizierung
Zum Einrichten des SharePoint Online-Indexers müssen Sie einige Schritte im Azure-Portal und einige Aktionen mithilfe der Vorschau-REST-API ausführen. Diese Vorschauversion wird nicht vom SDK unterstützt.

 Im folgenden Video wird gezeigt, wie Sie den SharePoint Online-Indexer einrichten.
 
> [!VIDEO https://www.youtube.com/embed/QmG65Vgl0JI]

### <a name="step-1-optional-enable-system-assigned-managed-identity"></a>Schritt 1 (optional): Aktivieren einer systemseitig zugewiesenen verwalteten Identität

Wenn eine systemseitig zugewiesene verwaltete Identität aktiviert wird, erstellt Azure eine Identität für Ihren Suchdienst, die vom Indexer verwendet werden kann. Diese Identität wird verwendet, um den Mandanten, in dem der Suchdienst bereitgestellt wird, automatisch zu erkennen.

Wenn sich die SharePoint Online-Website im gleichen Mandanten wie der Suchdienst befindet, müssen Sie die systemseitig zugewiesene verwaltete Identität für den Suchdienst aktivieren. Wenn sich die SharePoint Online-Website in einem anderen Mandanten als der Suchdienst befindet, muss die systemseitig zugewiesene verwaltete Identität nicht aktiviert werden.

![Aktivieren einer systemseitig zugewiesenen verwalteten Identität](media/search-howto-index-sharepoint-online/enable-managed-identity.png "Aktivieren einer systemseitig zugewiesenen verwalteten Identität")

Nach dem Auswählen von **Speichern** wird eine Objekt-ID angezeigt, die dem Suchdienst zugewiesen wurde.

![Systemseitig zugewiesene verwaltete Identität](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "Systemzugewiesene verwaltete Identität")

### <a name="step-2-create-an-aad-application"></a>Schritt 2: Erstellen einer AAD-Anwendung
Der SharePoint Online-Indexer verwendet diese Azure Active Directory-Anwendung (AAD) zur Authentifizierung. 

1.  Navigieren Sie zum [Azure-Portal](https://portal.azure.com/).

1.  Öffnen Sie das Menü auf der linken Seite der Hauptseite, wählen Sie **Azure Active Directory** aus, und klicken Sie dann auf **App-Registrierungen**. Wählen Sie **+ Neue Registrierung** aus.
    1.  Geben Sie einen Namen für Ihre App an.
    2.  Wählen Sie **Einzelner Mandant** aus.
    3.  Es ist kein Umleitungs-URI erforderlich.
    4.  Wählen Sie **Registrieren** aus.

1.  Wählen Sie im Menü auf der linken Seite **API-Berechtigungen** aus, und klicken Sie anschließend auf **Berechtigung hinzufügen**, auf **Microsoft Graph** und dann auf **Delegierte Berechtigungen**. Fügen Sie die folgenden API-Berechtigungen hinzu: 
    1.  **Delegiert – Files.Read.All** 
    2.  **Delegiert – Sites.Read.All** 
    3.  **Delegiert – User.Read**

    ![Delegierte API-Berechtigungen](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "Delegierte API-Berechtigungen")

    Bei der Verwendung von delegierten Berechtigungen greift der Indexer im Benutzerkontext auf die SharePoint-Website zu. Wenn Sie den Indexer ausführen, kann er also nur auf die Inhalte zugreifen, auf die der angemeldete Benutzer Zugriff hat. Die Benutzeranmeldung erfolgt beim Erstellen des Indexers oder beim Aktualisieren der Datenquelle. Die Anmeldung wird weiter unten in diesem Artikel beschrieben.

1.  Wählen Sie die Registerkarte **Authentifizierung** aus. Legen Sie **Öffentliche Clientflows zu lassen** auf **Ja** fest, und wählen Sie dann **Speichern** aus.

1.  Wählen Sie **+ Plattform hinzufügen** und dann **Mobilgerät- und Desktopanwendungen** aus, aktivieren Sie `https://login.microsoftonline.com/common/oauth2/nativeclient`, und klicken Sie auf **Konfigurieren**.

    ![Konfiguration der AAD-App-Authentifizierung](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "Konfiguration der AAD-App-Authentifizierung")

1.  Erteilen Sie die Administratoreinwilligung (nur für bestimmte Mandanten erforderlich).

    Einige Mandanten sind so gesperrt, dass die Administratoreinwilligung für diese delegierten API-Berechtigungen erforderlich ist. Wenn dies der Fall ist, muss ein Administrator vor dem Erstellen des Indexers die Administratoreinwilligung für die AAD-Anwendung erteilen.

    Da diese Anforderung nicht für alle Mandanten gilt, sollten Sie diesen Schritt zunächst überspringen und mit den weiteren Anweisungen fortfahren. Wenn die Authentifizierung beim Erstellen des Indexers fehlschlägt und Sie darauf hingewiesen werden, dass die Authentifizierung von einem Administrator genehmigt werden muss, wissen Sie, dass eine Administratoreinwilligung erforderlich ist. Bitten Sie in diesem Fall einen Mandantenadministrator, die Einwilligung mit der Schaltfläche unten zu erteilen.

    ![Erteilen der Administratoreinwilligung für die AAD-App](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "Erteilen der Administratoreinwilligung für die AAD-App")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>Schritt 3: Erstellen einer Datenquelle
> [!IMPORTANT] 
> Ab diesem Abschnitt müssen Sie die Vorschau-REST-API für die verbleibenden Schritte verwenden. Sehen Sie sich diesen [Schnellstart](search-get-started-rest.md) an, falls Sie nicht mit der REST-API für Azure Cognitive Search vertraut sind.

Eine Datenquelle gibt an, welche Daten indiziert werden müssen. Sie legt außerdem die Anmeldeinformationen für den Zugriff auf die Daten sowie die Richtlinien fest, mit denen Änderungen an den Daten effizient identifiziert werden können (z.B. neue, geänderte oder gelöschte Zeilen). Eine Datenquelle kann von mehreren Indexern im selben Suchdienst verwendet werden.

Für die SharePoint-Indizierung muss die Datenquelle über die folgenden erforderlichen Eigenschaften verfügen:
+ **name** ist der eindeutige Name der Datenquelle im Suchdienst.
+ **type** muss „sharepoint“ sein. Hierbei muss die Groß-/Kleinschreibung beachtet werden.
+ Mit **credentials** werden der SharePoint Online-Endpunkt und die ID der AAD-Anwendung (Client) angegeben. Ein Beispiel für einen SharePoint Online-Endpunkt ist `https://microsoft.sharepoint.com/teams/MySharePointSite`. Den SharePoint Online-Endpunkt erhalten Sie, indem Sie zur Startseite Ihrer SharePoint-Website navigieren und die URL aus dem Browser kopieren.
+ **container** gibt an, welche Dokumentbibliothek indiziert werden soll. Weitere Informationen zum Erstellen des Containers finden Sie weiter unten im Abschnitt [Steuern der indizierten Dokumente](#controlling-which-documents-are-indexed).

So erstellen Sie eine Datenquelle:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID];TenantId=[SharePoint Online site tenant id]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

> [!NOTE]
> Wenn sich die SharePoint Online-Website im gleichen Mandanten wie der Suchdienst befindet und die systemseitig zugewiesene verwaltete Identität aktiviert ist, muss `TenantId` nicht in die Verbindungszeichenfolge eingeschlossen werden. Wenn sich die SharePoint Online-Website in einem anderen Mandanten als der Suchdienst befindet, muss `TenantId` eingeschlossen werden.

### <a name="step-4-create-an-index"></a>Schritt 4: Erstellen eines Index
Mit dem Index werden die Felder in einem Dokument, Attribute und andere Konstrukte für die Suchoberfläche angegeben.

Der folgende Codeausschnitt zeigt, wie Sie einen Index mit einem durchsuchbaren Inhaltsfeld erstellen, um den aus Dokumenten in einer Dokumentbibliothek extrahierten Text zu speichern:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

> [!IMPORTANT]
> Nur [`metadata_spo_site_library_item_id`](#metadata) kann als Schlüsselfeld in einem vom SharePoint Online-Indexer erstellten Index verwendet werden. Wenn ein Schlüsselfeld in der Datenquelle nicht vorhanden ist, wird `metadata_spo_site_library_item_id` automatisch dem Schlüsselfeld zugeordnet.

Weitere Informationen finden Sie unter [Erstellen eines Index (REST-API)](/rest/api/searchservice/create-index).

### <a name="step-5-create-an-indexer"></a>Schritt 5: Erstellen eines Indexers
Ein Indexer verbindet eine Datenquelle mit einem Zielsuchindex und stellt einen Zeitplan zur Automatisierung der Datenaktualisierung bereit. Nachdem Sie den Index und die Datenquelle erstellt haben, können Sie den Indexer erstellen.

In diesem Abschnitt werden Sie aufgefordert, sich mit den Anmeldeinformationen für Ihre Organisation anzumelden, um Zugriff auf die SharePoint-Website zu erhalten. Erstellen Sie nach Möglichkeit ein neues Organisationsbenutzerkonto, und weisen Sie diesem neuen Benutzer die genauen Berechtigungen zu, die der Indexer besitzen soll.

Zum Erstellen des Indexers führen Sie die folgenden Schritte aus:

1.  Senden Sie eine Anforderung zum Erstellen des Indexers.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index",
          "fieldMappings" : [
            { 
              "sourceFieldName" : "metadata_spo_site_library_item_id", 
              "targetFieldName" : "id", 
              "mappingFunction" : { 
                "name" : "base64Encode" 
              } 
            }
          ]
        }
    
    ```

1.  Wenn Sie den Indexer zum ersten Mal erstellen, tritt der unten stehende Fehler auf. Öffnen Sie den Link in der Fehlermeldung. Wenn Sie nicht innerhalb von zehn Minuten zu diesem Link navigieren, läuft der Code ab, und Sie müssen die [Datenquelle](#create-data-source) neu erstellen.

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  Geben Sie den in der Fehlermeldung angezeigten Code an.

    ![Eingeben des Gerätecodes](media/search-howto-index-sharepoint-online/enter-device-code.png "Eingeben des Gerätecodes")

1.  Der SharePoint-Indexer greift als der angemeldete Benutzer auf den SharePoint-Inhalt zu. Der Benutzer, der sich in diesem Schritt anmeldet, ist der angemeldete Benutzer. Wenn Sie sich also mit einem Benutzerkonto anmelden, das keinen Zugriff auf ein Dokument in der Dokumentbibliothek hat, das Sie indizieren möchten, hat der Indexer keinen Zugriff auf dieses Dokument.

    Erstellen Sie nach Möglichkeit ein neues Benutzerkonto, und weisen Sie diesem neuen Benutzer die genauen Berechtigungen zu, die der Indexer besitzen soll.

1.  Genehmigen Sie die angeforderten Berechtigungen.

    ![Genehmigen der API-Berechtigungen](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "Genehmigen der API-Berechtigungen")

1.  Senden Sie die Anforderung zum Erstellen des Indexers erneut. Dieses Mal sollte die Anforderung erfolgreich ausgeführt werden. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>Schritt 6: Überprüfen des Indexerstatus
Nachdem der Indexer erstellt wurde, können Sie seinen Status überprüfen, indem Sie die folgende Anforderung senden.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

Weitere Informationen zum Indexerstatus finden Sie unter [Abrufen des Indexerstatus](/rest/api/searchservice/get-indexer-status).

## <a name="updating-the-data-source"></a>Aktualisieren der Datenquelle
Wenn keine Aktualisierungen des Datenquellenobjekts vorliegen, kann der Indexer ohne Benutzerinteraktion nach einem Zeitplan ausgeführt werden. Sie müssen sich jedoch bei jeder Aktualisierung des Azure Cognitive Search-Datenquellenobjekts erneut anmelden, damit der Indexer ausgeführt wird. Wenn Sie z. B. die Datenquellenabfrage ändern, müssen Sie sich erneut unter `https://microsoft.com/devicelogin` mit einem neuen Code anmelden.

Nachdem die Datenquelle aktualisiert wurde, führen Sie die folgenden Schritte aus:

1.  Starten Sie manuell eine Ausführung des Indexers.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    Weitere Informationen zur Ausführungsanforderung für den Indexer finden Sie unter [Ausführen des Indexers](/rest/api/searchservice/run-indexer).

1.  Überprüfen Sie den Indexerstatus. Wenn bei der letzten Indexerausführung ein Fehler aufgetreten ist und Sie dazu aufgefordert werden, zu `https://microsoft.com/devicelogin` zu wechseln, navigieren Sie zu dieser Seite, und geben Sie den neuen Code an. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    Weitere Informationen zum Indexerstatus finden Sie unter [Abrufen des Indexerstatus](/rest/api/searchservice/get-indexer-status).

1.  Anmeldename

1.  Starten Sie erneut manuell eine Ausführung des Indexers, und überprüfen Sie den Indexerstatus. Dieses Mal sollte die Indexerausführung erfolgreich gestartet werden.

<a name="metadata"></a>

## <a name="indexing-document-metadata"></a>Indizieren von Dokumentmetadaten
Wenn Sie den Indexer zum Indizieren von Dokumentmetadaten konfiguriert haben, können Sie die folgenden Metadaten indizieren.

| Bezeichner | type | BESCHREIBUNG | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | Der kombinierte Schlüssel aus Website-ID, Bibliotheks-ID und Element-ID, der ein Element in einer Dokumentbibliothek für eine Website eindeutig identifiziert. |
| metadata_spo_site_id | Edm.String | Die ID der SharePoint Online-Website. |
| metadata_spo_library_id | Edm.String | Die ID der Dokumentbibliothek. |
| metadata_spo_item_id | Edm.String | Die ID des Elements bzw. Dokumentelements in der Bibliothek. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | Das Datum/die Uhrzeit (UTC) der letzten Änderung des Elements. |
| metadata_spo_item_name | Edm.String | Name des Elements. |
| metadata_spo_item_size | Edm.Int64 | Die Größe (in Bytes) des Elements. | 
| metadata_spo_item_content_type | Edm.String | Der Inhaltstyp des Elements. | 
| metadata_spo_item_extension | Edm.String | Die Erweiterung des Elements. |
| metadata_spo_item_weburi | Edm.String | Der URI des Elements. |
| metadata_spo_item_path | Edm.String | Die Kombination aus dem übergeordneten Pfad und dem Elementnamen. | 

Der SharePoint Online-Indexer unterstützt auch spezifische Metadaten für die einzelnen Dokumenttypen. Weitere Informationen finden Sie unter [Metadateneigenschaften von Inhalten, die in Azure Cognitive Search verwendet werden](search-blob-metadata-properties.md).

> [!NOTE]
> Um benutzerdefinierte Metadaten zu indizieren, muss [`additionalColumns` in der Abfragedefinition](#query) angegeben werden.

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>Steuern der indizierten Dokumente
Ein einzelner SharePoint Online-Indexer kann Inhalte aus einer oder mehreren Dokumentbibliotheken indizieren. Verwenden Sie beim Erstellen Ihrer Datenquelle den Parameter *container*, um die Dokumentbibliotheken anzugeben, die Sie indizieren möchten. Der *Datenquellencontainer* verfügt über zwei Eigenschaften: *name* und *query*. 

### <a name="name"></a>Name
Die *name*-Eigenschaft ist erforderlich und muss einen der folgenden drei Werte aufweisen:
+ *defaultSiteLibrary*
    + Indiziert den gesamten Inhalt der Standarddokumentbibliothek der Website.
+   *allSiteLibraries*
    + Indiziert den gesamten Inhalt aller Dokumentbibliotheken in einer Website. Die Dokumentbibliotheken in einer Unterwebsite werden nicht indiziert. Diese können jedoch in der *query*-Eigenschaft angegeben werden.
+   *useQuery*
    + Indiziert nur den in der *Abfrage* definierten Inhalt.

<a name="query"></a>

### <a name="query"></a>Abfrage
Die *query*-Eigenschaft besteht aus Schlüsselwort-Wert-Paaren. Die unten aufgeführten Schlüsselwörter können verwendet werden. Die Werte sind entweder Website-URLs oder Dokumentbibliothek-URLs.

> [!NOTE]
> Um den Wert für ein bestimmtes Schlüsselwort zu ermitteln, empfehlen wir, SharePoint Online in einem Browser zu öffnen, zu der Dokumentbibliothek zu navigieren, die Sie ein- bzw. ausschließen möchten, und den URI aus dem Browser zu kopieren. Dies ist die einfachste Möglichkeit, um den Wert zur Verwendung mit einem Schlüsselwort in der Abfrage zu erhalten.

| Schlüsselwort | Abfragebeschreibung | Beispiel |
| ------------- | -------------- | ----------- |
| NULL | Wenn der Wert NULL oder leer ist, werden je nach Containername entweder die Standarddokumentbibliothek oder alle Dokumentbibliotheken indiziert. | So indizieren Sie den gesamten Inhalt der Standardbibliothek der Website: <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | Indiziert den Inhalt aller Bibliotheken in der definierten Website in der Verbindungszeichenfolge. Die Bibliotheken sind auf Unterwebsites Ihrer Website beschränkt. <br><br> Der *query*-Wert für dieses Schlüsselwort muss der URI der Website oder Unterwebsite sein. | So indizieren Sie den gesamten Inhalt aller Dokumentbibliotheken in „mysite“: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | Indiziert den Inhalt dieser Bibliothek. <br><br> Der *query*-Wert für dieses Schlüsselwort muss in einem der folgenden Formate angegeben werden: <br><br> Beispiel 1: <br><br> *includeLibrary=[Website oder Unterwebsite]/[Dokumentbibliothek]* <br><br> Beispiel 2: <br><br> Aus dem Browser kopierter URI. | So indizieren Sie den gesamten Inhalt von „MyDocumentLibrary“: <br><br> Beispiel 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> Beispiel 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  Schließt den Inhalt dieser Bibliothek von der Indizierung aus. <br><br> Der *query*-Wert für dieses Schlüsselwort muss in einem der folgenden Formate angegeben werden: <br><br> Beispiel 1: <br><br> *excludeLibrary=[Website- oder Unterwebsite-URI]/[Dokumentbibliothek]* <br><br> Beispiel 2: <br><br> Aus dem Browser kopierter URI. | So indizieren Sie den gesamten Inhalt aller Bibliotheken mit Ausnahme von „MyDocumentLibrary“: <br><br> Beispiel 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> Beispiel 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| additionalColumns | Indiziert Spalten aus dieser Bibliothek. <br><br> Der Abfragewert für dieses Schlüsselwort sollte eine durch Trennzeichen getrennte Liste von Spaltennamen enthalten, die Sie indizieren möchten. Verwenden Sie einen doppelten umgekehrten Schrägstrich, um Semikolons und Kommas in Spaltennamen mit Escapezeichen zu versehen: <br><br> Beispiel 1: <br><br> additionalColumns=MyCustomColumn,MyCustomColumn2 <br><br> Beispiel 2: <br><br> additionalColumns=MyCustomColumnWith\\,,MyCustomColumn2With\\; | So indizieren Sie den gesamten Inhalt von „MyDocumentLibrary“: <br><br> Beispiel 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary;additionalColumns=MyCustomColumn,MyCustomColumn2" } ``` <br><br> Beachten Sie die doppelten umgekehrten Schrägstriche, um Zeichen mit Escapezeichen zu versehen – JSON erfordert, dass ein umgekehrter Schrägstrich mit einem anderen umgekehrten Schrägstrich als Escapezeichen versehen wird. <br><br> Beispiel 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx;additionalColumns=MyCustomColumnWith\\,,MyCustomColumnWith\\;" } ``` |

## <a name="index-by-file-type"></a>Indizieren nach Dateityp
Sie können steuern, welche Dokumente indiziert und welche übersprungen werden.

### <a name="include-documents-having-specific-file-extensions"></a>Einschließen von Dokumenten mit bestimmten Dateierweiterungen
Sie können nur die Dokumente mit den Dateinamenerweiterungen indizieren, die Sie mit dem Konfigurationsparameter `indexedFileNameExtensions` des Indexers angeben. Der Wert ist eine Zeichenfolge mit einer durch Trennzeichen getrennte Liste von Dateierweiterungen (mit einem vorangestellten Punkt). So indizieren Sie beispielsweise nur die PDF- und DOCX-Dokumente:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Ausschließen von Dokumenten mit bestimmten Dateierweiterungen
Mit dem Konfigurationsparameter `excludedFileNameExtensions` können Sie Dokumente mit bestimmten Dateinamenerweiterungen von der Indizierung ausschließen. Der Wert ist eine Zeichenfolge mit einer durch Trennzeichen getrennte Liste von Dateierweiterungen (mit einem vorangestellten Punkt). So indizieren Sie beispielsweise alle Dokumente mit Ausnahme von Dokumenten mit den Erweiterungen PNG und JPEG:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Wenn sowohl der `indexedFileNameExtensions`- als auch der `excludedFileNameExtensions`-Parameter vorhanden ist, wird in der kognitiven Azure-Suche zunächst `indexedFileNameExtensions` und danach `excludedFileNameExtensions` überprüft. Das heißt, wenn die gleiche Dateierweiterung in beiden Listen vorhanden ist, wird sie von der Indizierung ausgeschlossen.

## <a name="handling-errors"></a>Behandlung von Fehlern
Der SharePoint Online-Indexer wird standardmäßig beendet, sobald er auf ein Dokument mit einem nicht unterstützten Inhaltstyp trifft (z. B. ein Bild). Natürlich können Sie den Parameter `excludedFileNameExtensions` nutzen, um bestimmte Inhaltstypen zu überspringen. Allerdings müssen Sie möglicherweise Dokumente indizieren, ohne im Voraus alle möglichen Inhaltstypen zu kennen. Um die Indizierung bei einem nicht unterstützten Inhaltstyp fortzusetzen, setzen Sie den Konfigurationsparameter `failOnUnsupportedContentType` auf FALSE:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

Es kann vorkommen, dass Azure Cognitive Search den Inhaltstyp einiger Dokumenten nicht bestimmen oder ein Dokument mit einem eigentlich unterstützten Inhaltstyp nicht verarbeiten kann. Um diesen Fehlermodus zu ignorieren, legen Sie den Konfigurationsparameter `failOnUnprocessableDocument` auf FALSE fest:

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Cognitive Search beschränkt die Größe der indizierten Dokumente. Diese Grenzwerte sind unter [Dienstgrenzwerte in der kognitiven Azure-Suche](./search-limits-quotas-capacity.md) angegeben. Zu große Dokumente werden standardmäßig als Fehler behandelt. Wenn Sie den Konfigurationsparameter `indexStorageMetadataOnlyForOversizedDocuments` auf TRUE setzen, können Sie Speichermetadaten von zu großen Dokumenten jedoch weiterhin indizieren:

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Sie können die Indizierung auch fortsetzen, wenn bei der Verarbeitung während der Analyse von Dokumenten oder beim Hinzufügen von Dokumenten zu einem Index Fehler auftreten. Um eine bestimmte Anzahl von Fehlern zu ignorieren, legen Sie die Konfigurationsparameter `maxFailedItems` und `maxFailedItemsPerBatch` auf die gewünschten Werte fest. Beispiel:

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>Weitere Informationen
+ [Indexer in der kognitiven Azure-Suche](search-indexer-overview.md)
+ [Metadateneigenschaften von Inhalten, die in Azure Cognitive Search verwendet werden](search-blob-metadata-properties.md)

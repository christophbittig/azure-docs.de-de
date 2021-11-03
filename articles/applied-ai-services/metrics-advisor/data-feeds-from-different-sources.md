---
title: Verbinden von unterschiedlichen Datenquellen mit Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Hinzufügen unterschiedlicher Datenfeeds zu Metrics Advisor
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: mbullwin
ms.openlocfilehash: 005886c0399a89fe2e58aa669a8f7c0d33821cd3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026636"
---
# <a name="how-to-connect-different-data-sources"></a>Vorgehensweise: Verbinden verschiedener Datenquellen

Verwenden Sie diesen Artikel, um die Einstellungen und Anforderungen zum Herstellen einer Verbindung von verschiedenen Arten von Datenquellen mit Azure Metrics Advisor zu ermitteln. Informationen zur Verwendung von Daten mit Metrics Advisor finden Sie unter [Onboarding von Daten](how-tos/onboard-your-data.md). 

## <a name="supported-authentication-types"></a>Unterstützte Authentifizierungstypen

| Authentifizierungstypen | BESCHREIBUNG |
| ---------------------|-------------|
|**Grundlegend** | Sie müssen grundlegende Parameter für den Zugriff auf Datenquellen bereitstellen. Beispielsweise können Sie eine Verbindungszeichenfolge oder ein Kennwort verwenden. Datenfeedadministratoren können diese Anmeldeinformationen anzeigen. |
| **Verwaltete Azure-Identität** | [Verwaltete Identitäten](../../active-directory/managed-identities-azure-resources/overview.md) für Azure-Ressourcen bezeichnen ein Feature von Azure Active Directory (Azure AD). Sie stellt für Azure-Dienste eine automatisch verwaltete Identität in Azure AD bereit. Sie können die Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt.|
| **Azure SQL-Verbindungszeichenfolge**| Speichern Sie die Azure SQL-Verbindungszeichenfolge als Anmeldeinformationsentität in Metrics Advisor, und verwenden Sie sie beim Importieren von Metrikdaten jedes Mal direkt. Nur Administratoren der Anmeldeinformationsentität können diese Anmeldeinformationen anzeigen, aber autorisierte anzeigende Benutzer können Datenfeeds erstellen, ohne dass sie die Details der Anmeldeinformationen kennen müssen. |
| **Gemeinsam genutzter Schlüssel für Azure Data Lake Storage Gen2**| Speichern Sie den Data Lake-Kontoschlüssel als Anmeldeinformationsentität in Metrics Advisor, und verwenden Sie sie direkt bei jedem Importieren von Metrikdaten. Nur Administratoren der Anmeldeinformationsentität können diese Anmeldeinformationen anzeigen, aber autorisierte anzeigende Benutzer können Datenfeeds erstellen, ohne dass sie die Details der Anmeldeinformationen kennen müssen.|
| **Dienstprinzipal**| Speichern Sie den [Dienstprinzipal](../../active-directory/develop/app-objects-and-service-principals.md) als Anmeldeinformationsentität in Metrics Advisor, und verwenden Sie sie beim Importieren von Metrikdaten jedes Mal direkt. Nur Administratoren der Anmeldeinformationsentität können diese Anmeldeinformationen anzeigen, aber autorisierte anzeigende Benutzer können Datenfeeds erstellen, ohne dass sie die Details der Anmeldeinformationen kennen müssen.|
| **Dienstprinzipal vom Schlüsseltresor**|Speichern Sie den [Dienstprinzipal als Anmeldeinformationsentität in einem Schlüsseltresor](/azure-stack/user/azure-stack-key-vault-store-credentials) in Metrics Advisor, und verwenden Sie sie beim Importieren von Metrikdaten jedes Mal direkt. Nur Administratoren der Anmeldeinformationsentität können diese Anmeldeinformationen anzeigen, aber anzeigende Benutzer können Datenfeeds erstellen, ohne dass sie die Details der Anmeldeinformationen kennen müssen. |


## <a name="data-sources-and-corresponding-authentication-types"></a>Datenquellen und zugehörige Authentifizierungstypen

| Datenquellen | Authentifizierungstypen |
|-------------| ---------------------|
|[Application Insights](#appinsights) | Basic |
|[Azure Blob Storage (JSON)](#blob) | Basic<br>Verwaltete Identität |
|[Azure Cosmos DB (SQL)](#cosmosdb) | Basic |
|[Azure Data Explorer (Kusto)](#kusto) | Basic<br>Verwaltete Identität<br>Dienstprinzipal<br>Dienstprinzipal vom Schlüsseltresor |
|[Azure Data Lake Storage Gen2](#adl) | Basic<br>Gemeinsam genutzter Schlüssel für Data Lake Storage Gen2<br>Dienstprinzipal<br>Dienstprinzipal vom Schlüsseltresor |
|[Azure Event Hubs](#eventhubs) | Basic |
|[Azure Monitor-Protokolle](#log) | Basic<br>Dienstprinzipal<br>Dienstprinzipal vom Schlüsseltresor |
|[Azure SQL-Datenbank/SQL Server](#sql) | Basic<br>Verwaltete Identität<br>Dienstprinzipal<br>Dienstprinzipal vom Schlüsseltresor<br>Azure SQL-Verbindungszeichenfolge |
|[Azure Table Storage](#table) | Basic | 
|[InfluxDB (InfluxQL)](#influxdb) | Basic |
|[MongoDB](#mongodb) | Basic |
|[MySQL](#mysql) | Basic |
|[PostgreSQL](#pgsql) | Basic|
|[Lokale Dateien (CSV)](#csv) | Basic|

In den folgenden Abschnitten werden die Parameter angegeben, die für alle Authentifizierungstypen in verschiedenen Datenquellenszenarios erforderlich sind. 

## <a name="span-idappinsightsapplication-insightsspan"></a><span id="appinsights">Application Insights</span>

* **Anwendungs-ID**: Diese wird dazu verwendet, die betreffende Anwendung zu identifizieren, wenn die Application Insights-API verwendet wird. Führen Sie die folgenden Schritte aus, um die Anwendungs-ID abzurufen:

   1. Wählen Sie in Ihrer Application Insights-Ressource die Option **API-Zugriff** aus.
   
      ![Screenshot: Abrufen der Anwendungs-ID aus der Application Insights-Ressource](media/portal-app-insights-app-id.png)

   2. Kopieren Sie die generierte Anwendungs-ID in Metrics Advisor in das Feld **Anwendungs-ID**. 

* **API-Schlüssel**: API-Schlüssel werden von Anwendungen außerhalb des Browsers für den Zugriff auf diese Ressource verwendet. Führen Sie zum Abrufen des API-Schlüssels die folgenden Schritte aus:

   1. Wählen Sie in der Application Insights-Ressource die Option **API-Zugriff** aus.

   2. Wählen Sie **API-Schlüssel erstellen** aus.

   3. Geben Sie eine kurze Beschreibung ein, wählen Sie die Option **Telemetrie lesen** und dann die Option **Schlüssel generieren** aus.

      ![Screenshot: Abrufen des API-Schlüssels im Azure-Portal](media/portal-app-insights-app-id-api-key.png)

       > [!IMPORTANT]
       > Kopieren und speichern Sie diesen API-Schlüssel. Der Schlüssel wird nicht erneut angezeigt. Wenn Sie diesen Schlüssel verlieren, müssen Sie einen neuen erstellen.

   4. Kopieren Sie den API-Schlüssel in das Feld **API-Schlüssel** in Metrics Advisor.

* **Abfrage**: Application Insights-Protokolle basieren auf Azure Data Explorer, und Azure Monitor-Protokollabfragen verwenden eine Version derselben Kusto-Abfragesprache. Die [Dokumentation zur Kusto-Abfragesprache](/azure/data-explorer/kusto/query) ist die wichtigste Ressource zum Schreiben einer Abfrage für Application Insights. 

    Beispielabfrage:

    ``` Kusto
    [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);
    ```
    Unter [Tutorial: Schreiben einer gültigen Abfrage](tutorials/write-a-valid-query.md) finden Sie konkretere Beispiele.
  
## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage (JSON)</span>

* **Verbindungszeichenfolge**: Es gibt zwei Authentifizierungstypen für Azure Blob Storage (JSON):

    * [Basic](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account): Informationen zum Abrufen dieser Zeichenfolge finden Sie unter **Konfigurieren von Azure Storage-Verbindungszeichenfolgen**. Sie können auch das Azure-Portal für Ihre Azure Blob Storage-Ressource besuchen und die Verbindungszeichenfolge direkt in **Einstellungen** > **Zugriffsschlüssel** ermitteln.
    
    * **Verwaltete Identität**: Verwaltete Identitäten für Azure-Ressourcen können den Zugriff auf Blob- und Warteschlangendaten autorisieren. Für das Feature werden Azure AD-Anmeldeinformationen aus Anwendungen verwendet, die auf virtuellen Azure-Computern ausgeführt werden, sowie aus Funktions-Apps, VM-Skalierungsgruppen und anderen Diensten. 
    
        Sie können eine verwaltete Identität im Azure-Portal für Ihre Azure Blob Storage-Ressource erstellen. Wählen Sie unter **Zugriffssteuerung (IAM)** die Option **Rollenzuweisungen** und dann **Hinzufügen** aus. Ein empfohlener Rollentyp ist **Storage-Blobdatenleser**. Weitere Informationen finden Sie unter [Verwenden der verwalteten Identität für den Zugriff auf Azure Storage](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access-1).
    
        ![Screenshot: Blob einer verwalteten Identität](media/managed-identity-blob.png)
    

* **Container**: Metrics Advisor erwartet, dass Zeitreihendaten als Blobdateien (ein Blob pro Zeitstempel) unter einem einzelnen Container gespeichert sind. Dies ist das Feld für den Containernamen.

* **Blobvorlage**: Metrics Advisor verwendet einen Pfad, um die JSON-Datei in Blob Storage zu suchen. Dies ist ein Beispiel für eine Blobdateivorlage, die dazu verwendet wird, die JSON-Datei in Blob Storage zu finden: `%Y/%m/FileName_%Y-%m-%d-%h-%M.json`. `%Y/%m` ist der Pfad, und wenn Sie `%d` im Pfad haben, können Sie ihn nach `%m` hinzufügen. Wenn die JSON-Datei nach Datum benannt wird, können Sie auch `%Y-%m-%d-%h-%M.json` verwenden.

   Die folgenden Parameter werden unterstützt:
   
   * `%Y` ist das Jahr, formatiert als `yyyy`.
   * `%m` ist der Monat, formatiert als `MM`.
   * `%d` ist der Tag, formatiert als `dd`.
   * `%h` ist die Stunde, formatiert als `HH`.
   * `%M` ist die Minute, formatiert als `mm`.
  
   Im folgenden Dataset sollte die Blobvorlage beispielsweise `%Y/%m/%d/00/JsonFormatV2.json` sein.
  
   ![Screenshot: Blobvorlage](media/blob-template.png)
  

* **JSON-Formatversion**: Definiert das Datenschema in den JSON-Dateien. Metrics Advisor unterstützt die folgenden Versionen. Sie können eine auswählen, um das Feld auszufüllen:
  
   * **v1** (Standardwert)

      Nur die Metriken *Name* und *Wert* werden akzeptiert. Zum Beispiel:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

   * **v2**

      Die Metriken *Dimensionen* und *Zeitstempel* werden ebenfalls akzeptiert. Zum Beispiel:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

   Pro JSON-Datei ist nur ein Zeitstempel zulässig. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Verbindungszeichenfolge**: Die Verbindungszeichenfolge für den Zugriff auf Azure Cosmos DB. Diese finden Sie in der Azure Cosmos DB-Ressource im Azure-Portal unter **Schlüssel**. Weitere Informationen finden Sie unter [Sicherer Zugriff auf Daten in Azure Cosmos DB](../../cosmos-db/secure-access-to-data.md).
* **Datenbank**: Die Datenbank, die abgefragt werden soll Navigieren Sie im Azure-Portal unter **Container** zu **Durchsuchen**, um die Datenbank zu suchen.
* **Sammlungs-ID**: Die Sammlungs-ID, die abgefragt werden soll. Navigieren Sie im Azure-Portal unter **Container** zu **Durchsuchen**, um die Sammlungs-ID zu suchen.
* **SQL-Abfrage**: Eine SQL-Abfrage zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten. Verwenden Sie die Variablen `@IntervalStart` und `@IntervalEnd` in der Abfrage. Sie müssen wie folgt formatiert werden: `yyyy-MM-ddTHH:mm:ssZ`.

    Beispielabfrage:
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```

    Weitere Informationen finden Sie im [Tutorial zum Schreiben einer gültigen Abfrage](tutorials/write-a-valid-query.md).

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **Verbindungszeichenfolge**: Es gibt vier Authentifizierungstypen für Azure Data Explorer (Kusto): Basic, Dienstprinzipal, Dienstprinzipal aus Key Vault und verwaltete Identität. Die Datenquelle in der Verbindungszeichenfolge muss das URI-Format aufweisen (beginnt mit „https“). Sie können den URI im Azure-Portal finden.
    
    * **Basic**: Metrics Advisor unterstützt den Zugriff auf Azure Data Explorer (Kusto) über die Azure AD-Anwendungsauthentifizierung. Sie müssen eine Azure AD-Anwendung erstellen und registrieren und diese anschließend für den Zugriff auf eine Azure Data Explorer-Datenbank autorisieren. Weitere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendungsregistrierung in Azure Data Explorer](/azure/data-explorer/provision-azure-ad-app). Hier ist ein Beispiel für eine Verbindungszeichenfolge:
        
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>;AAD Federated Security=True;Application Client ID=<Application Client ID>;Application Key=<Application Key>;Authority ID=<Tenant ID>
        ```

    * **Dienstprinzipal**: Ein Dienstprinzipal ist eine konkrete Instanz, die aus dem Anwendungsobjekt erstellt wurde. Der Dienstprinzipal erbt bestimmte Eigenschaften von diesem Anwendungsobjekt. Das Dienstprinzipalobjekt definiert, was die App tatsächlich im jeweiligen Mandanten ausführen kann, wer auf die App zugreifen kann und auf welche Ressourcen die App zugreifen kann. Einen Dienstprinzipal verwenden Sie in Metrics Advisor wie folgt:
    
        1. Erstellen Sie die Azure Active Directory-Anwendungsregistrierung. Weitere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendungsregistrierung in Azure Data Explorer](/azure/data-explorer/provision-azure-ad-app).

        1. Verwalten Sie die Berechtigungen für Datenbanken in Azure-Daten-Explorer. Weitere Informationen finden Sie unter [Verwalten der Berechtigungen für Datenbanken in Azure Data Explorer](/azure/data-explorer/manage-database-permissions). 

        1. Erstellen Sie eine Entität mit Anmeldeinformationen in Metrics Advisor. Hier erfahren Sie, wie Sie eine [Entität mit Anmeldeinformationen in Metrics Advisor erstellen](how-tos/credential-entity.md), damit Sie diese Entität auswählen können, wenn Sie einen Datenfeed für den Dienstprinzipal-Authentifizierungstyp hinzufügen. 
        
        Hier ist ein Beispiel für eine Verbindungszeichenfolge:
        
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

    * **Dienstprinzipal aus Key Vault**: Azure Key Vault hilft beim Schützen von kryptografischen Schlüsseln und Geheimniswerten, die von Cloudanwendungen und -diensten verwendet werden. Mit Key Vault können Sie Schlüssel und Geheimniswerte verschlüsseln. Sie müssen zuerst einen Dienstprinzipal erstellen und den Dienstprinzipal dann in Key Vault speichern. Weitere Informationen finden Sie unter [Erstellen einer Anmeldeinformationsentität für einen Dienstprinzipal aus Key Vault](how-tos/credential-entity.md#sp-from-kv). Führen Sie die ausführlich erläuterten Verfahrensschritte durch, um den Dienstprinzipal aus Key Vault festzulegen. Hier ist ein Beispiel für eine Verbindungszeichenfolge: 
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

    * **Verwaltete Identität**: Eine verwaltete Identität für Azure-Ressourcen kann den Zugriff auf Blob- und Warteschlangendaten autorisieren. Die verwaltete Identität verwendet Azure AD-Anmeldeinformationen aus Anwendungen, die auf virtuellen Azure-Computern ausgeführt werden, sowie aus Funktions-Apps, VM-Skalierungsgruppen und anderen Diensten. Durch Verwendung einer verwalteten Identität für Azure-Ressourcen zusammen mit der Azure AD-Authentifizierung können Sie vermeiden, dass Anmeldeinformationen mit den in der Cloud ausgeführten Anwendungen gespeichert werden. Hier erfahren Sie, wie Sie eine [Autorisierung mit einer verwalteten Identität durchführen](../../storage/blobs/authorize-managed-identity.md#enable-managed-identities-on-a-vm). 
    
        Sie können eine verwaltete Identität im Azure-Portal für Azure Data Explorer (Kusto) erstellen. Klicken Sie auf **Berechtigungen** > **Hinzufügen**. Der empfohlene Rollentyp lautet: **Administrator/Anzeigender Benutzer**.
        
        ![Screenshot: verwaltete Identität für Kusto](media/managed-identity-kusto.png)

        Hier ist ein Beispiel für eine Verbindungszeichenfolge: 
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

     
* **Abfrage**: Unter [Kusto-Abfragesprache](/azure/data-explorer/kusto/query) finden Sie Informationen zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten. Verwenden Sie die Variablen `@IntervalStart` und `@IntervalEnd` in der Abfrage. Sie müssen wie folgt formatiert werden: `yyyy-MM-ddTHH:mm:ssZ`.

  Beispielabfrage:
    
  ```kusto
  [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);    
  ```

  Weitere Informationen finden Sie im [Tutorial zum Schreiben einer gültigen Abfrage](tutorials/write-a-valid-query.md).

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **Kontoname**: Die Authentifizierungstypen für Azure Data Lake Storage Gen2 sind Basic, Gemeinsam verwendeter Azure Data Lake Storage Gen2-Schlüssel, Dienstprinzipal und Dienstprinzipal aus Key Vault.
    
    * **Basic**: Der **Kontoname** Ihres Azure Data Lake Storage Gen2-Kontos. Diesen finden Sie in der Azure Storage-Kontoressource (Azure Data Lake Storage Gen2) unter **Zugriffsschlüssel**. 

    * **Gemeinsam verwendeter Azure Data Lake Storage Gen2-Schlüssel**: Geben Sie zunächst den Kontoschlüssel für den Zugriff auf Azure Data Lake Storage Gen2 an (dies entspricht dem Kontoschlüssel im Authentifizierungstyp „Basic“. Diesen finden Sie in der Azure Storage-Kontoressource (Azure Data Lake Storage Gen2) unter **Zugriffsschlüssel**. Anschließend [erstellen Sie eine Anmeldeinformationsentität](how-tos/credential-entity.md) für den Typ eines gemeinsam verwendeten Azure Data Lake Storage Gen2-Schlüssels und geben den Kontoschlüssel ein. 

        Der Kontoname entspricht dem Authentifizierungstyp „Basic“.
    
    * **Dienstprinzipal**: Ein *Dienstprinzipal* ist eine konkrete Instanz, die aus dem Anwendungsobjekt erstellt wird und bestimmte Eigenschaften von diesem Anwendungsobjekt erbt. Ein Dienstprinzipal wird in jedem Mandanten erstellt, in dem die Anwendung verwendet wird, und verweist auf das global eindeutige Anwendungsobjekt. Das Dienstprinzipalobjekt definiert, was die App tatsächlich im jeweiligen Mandanten ausführen kann, wer auf die App zugreifen kann und auf welche Ressourcen die App zugreifen kann.

        Der Kontoname entspricht dem Authentifizierungstyp „Basic“.
    
        **Schritt 1**: Erstellen und registrieren Sie eine Azure AD-Anwendung, und autorisieren Sie sie dann für den Zugriff auf die Datenbank. Weitere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendungsregistrierung](/azure/data-explorer/provision-azure-ad-app).

        **Schritt 2**: Weisen Sie Rollen zu.
        
        1. Navigieren Sie im Azure-Portal zum **Speicherkonten**-Dienst.
        
        2. Wählen Sie das Azure Data Lake Storage Gen2-Konto für diese Anwendungsregistrierung aus.

        3. Wählen Sie **Access Control (IAM)** aus.

        4. Wählen Sie **+ Hinzufügen** und dann im Menü die Option **Rollenzuweisung hinzufügen** aus.

        5. Legen Sie das Feld **Auswählen** auf den Azure AD-Anwendungsnamen und die Rolle auf **Mitwirkender an Storage-Blobdaten** fest. Klicken Sie dann auf **Speichern**.
        
        ![Screenshot: Schritte zum Zuweisen von Rollen](media/datafeeds/adls-gen-2-app-reg-assign-roles.png)

        **Schritt 3**: [Erstellen Sie eine Anmeldeinformationsentität](how-tos/credential-entity.md) in Metrics Advisor, damit Sie diese Entität auswählen können, wenn Sie einen Datenfeed für den Dienstprinzipal-Authentifizierungstyp hinzufügen. 
        
    * **Dienstprinzipal aus Key Vault**: Key Vault hilft, kryptografische Schlüssel und Geheimniswerte zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Mit Key Vault können Sie Schlüssel und Geheimniswerte verschlüsseln. Erstellen Sie zuerst einen Dienstprinzipal, und speichern Sie den Dienstprinzipal dann in einem Schlüsseltresor. Weitere Informationen finden Sie unter [Erstellen einer Anmeldeinformationsentität für den Dienstprinzipal aus Key Vault](how-tos/credential-entity.md#sp-from-kv). Der Kontoname entspricht dem Authentifizierungstyp „Basic“.

* **Kontoschlüssel** (nur für den Authentifizierungstyp „Basic“ erforderlich): Geben Sie den Kontoschlüssel für den Zugriff auf Azure Data Lake Storage Gen2 an. Diesen finden Sie in der Azure Storage-Kontoressource (Azure Data Lake Storage Gen2) unter **Zugriffsschlüssel**.

* **Dateisystemname (Container)** : Für Metrics Advisor speichern Sie Zeitreihendaten als Blobdateien (ein Blob pro Zeitstempel) unter einem einzelnen Container. Dies ist das Feld für den Containernamen. Sie finden es in der Azure Storage-Kontoinstanz (Azure Data Lake Storage Gen2). Wählen Sie in **Data Lake Storage** die Option **Container** aus. Dann wird der Name des Containers angezeigt.

* **Verzeichnisvorlage**: Dies ist die Verzeichnisvorlage der Blobdatei. Die folgenden Parameter werden unterstützt:

   * `%Y` ist das Jahr, formatiert als `yyyy`.
   * `%m` ist der Monat, formatiert als `MM`.
   * `%d` ist der Tag, formatiert als `dd`.
   * `%h` ist die Stunde, formatiert als `HH`.
   * `%M` ist die Minute, formatiert als `mm`.

   Abfragebeispiel für eine tägliche Metrik: `%Y/%m/%d`.

   Abfragebeispiel für eine stündliche Metrik: `%Y/%m/%d/%h`.

* **Dateivorlage**: Von Metrics Advisor wird ein Pfad dazu verwendet, in Blob Storage nach der JSON-Datei zu suchen. Im Folgenden finden Sie ein Beispiel für eine Blobdateivorlage, die dazu verwendet wird, in Blob Storage nach der JSON-Datei zu suchen: `%Y/%m/FileName_%Y-%m-%d-%h-%M.json`. `%Y/%m` ist der Pfad, und wenn Sie `%d` im Pfad haben, können Sie ihn nach `%m` hinzufügen. 
   
   Die folgenden Parameter werden unterstützt:
   
   * `%Y` ist das Jahr, formatiert als `yyyy`.
   * `%m` ist der Monat, formatiert als `MM`.
   * `%d` ist der Tag, formatiert als `dd`.
   * `%h` ist die Stunde, formatiert als `HH`.
   * `%M` ist die Minute, formatiert als `mm`.

   Metrics Advisor unterstützt das Datenschema in den JSON-Dateien wie im folgenden Beispiel:

   ``` JSON
   [
     {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
     {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
   ]
   ```

## <a name="span-ideventhubsazure-event-hubsspan"></a><span id="eventhubs">Azure Event Hubs</span>

* **Einschränkungen**: Beachten Sie die folgenden Einschränkungen bei der Integration.

   * Die Metrics Advisor-Integration mit Event Hubs unterstützt derzeit nicht mehr als drei aktive Datenfeeds in einer einzigen Metrics Advisor-Instanz in der Public Preview.
   * Metrics Advisor beginnt immer mit der Verarbeitung von Nachrichten aus dem letzten Offset, einschließlich der Reaktivierung eines angehaltenen Datenfeeds.
   
      * Nachrichten während der Pausenphase des Datenfeeds gehen verloren.
      * Die Startzeit der Datenerfassung für den Datenfeed wird automatisch auf den aktuellen Zeitstempel nach koordinierter Weltzeit festgelegt, wenn der Datenfeed erstellt wird. Diese Zeit dient nur zu Referenzzwecken.

   * Pro Consumergruppe kann nur ein einziger Datenfeed verwendet werden. Zum Wiederverwenden einer Consumergruppe aus einem anderen gelöschten Datenfeed müssen Sie nach dem Löschen mindestens zehn Minuten warten.
   * Die Verbindungszeichenfolge und die Consumergruppe können nach dem Erstellen des Datenfeeds nicht mehr geändert werden.
   * Für Event Hubs-Nachrichten wird nur JSON unterstützt, und die JSON-Werte können kein geschachteltes JSON-Objekt sein. Das Element der obersten Ebene kann ein JSON-Objekt oder ein JSON-Array sein.
    
    Die folgenden Nachrichten sind gültig:

    ``` JSON
    Single JSON object 
    {
    "metric_1": 234, 
    "metric_2": 344, 
    "dimension_1": "name_1", 
    "dimension_2": "name_2"
    }
    ```
        
    ``` JSON
    JSON array 
    [
        {
            "timestamp": "2020-12-12T12:00:00", "temperature": 12.4,
            "location": "outdoor"
        },
        {
            "timestamp": "2020-12-12T12:00:00", "temperature": 24.8,
            "location": "indoor"
        }
    ]
    ```


* **Verbindungszeichenfolge**: Wechseln Sie zur Instanz Event Hubs. Fügen Sie dann eine neue Richtlinie hinzu, oder wählen Sie eine vorhandene SAS-Richtlinie (Richtlinie für den gemeinsamen Zugriff) aus. Kopieren Sie die Verbindungszeichenfolge im Popupbereich.
    ![Screenshot: Event Hubs](media/datafeeds/entities-eventhubs.jpg)
    
    ![Screenshot: SAS-Richtlinien](media/datafeeds/shared-access-policies.jpg)

    Hier ist ein Beispiel für eine Verbindungszeichenfolge: 
    ```
    Endpoint=<Server>;SharedAccessKeyName=<SharedAccessKeyName>;SharedAccessKey=<SharedAccess Key>;EntityPath=<EntityPath>
    ```

* **Consumergruppe**: Eine [Consumergruppe](../../event-hubs/event-hubs-features.md#consumer-groups) ist eine Ansicht (Status, Position oder Offset) eines gesamten Event Hubs.
Sie finden sie im Menü **Consumergruppen** einer Instanz von Azure Event Hubs. Eine Consumergruppe kann nur einen einzigen Datenfeed verarbeiten. Erstellen Sie für jeden Datenfeed eine neue Consumergruppe.
* **Zeitstempel** (optional): Metrics Advisor verwendet den Event Hubs-Zeitstempel als Ereigniszeitstempel, wenn die Benutzerdatenquelle kein Zeitstempelfeld enthält. Das Zeitstempelfeld ist optional. Wenn keine Zeitstempelspalte ausgewählt wird, verwendet der Dienst den Zeitpunkt der Einreihung in die Warteschlange als Zeitstempel.

    Das Zeitstempelfeld muss mit einem der beiden folgenden Formate übereinstimmen:
    
    * `YYYY-MM-DDTHH:MM:SSZ`
    * Die Anzahl von Sekunden oder Millisekunden ab der Epoche von `1970-01-01T00:00:00Z`.
    
    Der Zeitstempel wird links an der Granularität ausgerichtet. Wenn der Zeitstempel beispielsweise `2019-01-01T00:03:00Z` lautet, beträgt die Granularität 5 Minuten, und Metrics Advisor richtet den Zeitstempel an `2019-01-01T00:00:00Z` aus. Wenn der Ereigniszeitstempel `2019-01-01T00:10:00Z` ist, wird der Zeitstempel von Metrics Advisor direkt ohne Ausrichtung verwendet. 


## <a name="span-idlogazure-monitor-logsspan"></a><span id="log">Azure Monitor-Protokolle</span>

Azure Monitor-Protokolle weisen die folgenden Authentifizierungstypen auf: Basic, Dienstprinzipal und Dienstprinzipal aus Key Vault.
* **Basic**: Sie müssen die Werte für **Mandanten-ID**, **Client-ID**, **Geheimer Clientschlüssel** und **Arbeitsbereichs-ID** ausfüllen.
   Informationen zum Abrufen von **Mandanten-ID**, **Client-ID** und **Geheimer Clientschlüssel** finden Sie unter [Registrieren von Anwendung oder Web-API](../../active-directory/develop/quickstart-register-app.md). Die **Arbeitsbereichs-ID** finden Sie im Azure-Portal.
   
    ![Screenshot: Ort der Arbeitsbereichs-ID im Azure-Portal](media/workspace-id.png)
    
* **Dienstprinzipal**: Ein Dienstprinzipal ist eine konkrete Instanz, die aus dem Anwendungsobjekt erstellt wird und bestimmte Eigenschaften von diesem Anwendungsobjekt erbt. Ein Dienstprinzipal wird in jedem Mandanten erstellt, in dem die Anwendung verwendet wird, und verweist auf das global eindeutige Anwendungsobjekt. Das Dienstprinzipalobjekt definiert, was die App tatsächlich im jeweiligen Mandanten ausführen kann, wer auf die App zugreifen kann und auf welche Ressourcen die App zugreifen kann.
    
    **Schritt 1**: Erstellen und registrieren Sie eine Azure AD-Anwendung, und autorisieren Sie diese dann für den Zugriff auf eine Datenbank. Weitere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendungsregistrierung](/azure/data-explorer/provision-azure-ad-app).

    **Schritt 2**: Weisen Sie Rollen zu.
    1. Navigieren Sie im Azure-Portal zum **Speicherkonten**-Dienst.
    2. Wählen Sie **Access Control (IAM)** aus.
    3. Wählen Sie **+ Hinzufügen** und dann im Menü die Option **Rollenzuweisung hinzufügen** aus.
    4. Legen Sie das Feld **Auswählen** auf den Azure AD-Anwendungsnamen und die Rolle auf **Mitwirkender an Storage-Blobdaten** fest. Klicken Sie dann auf **Speichern**.
    
        ![Screenshot: Zuweisen von Rollen](media/datafeeds/adls-gen-2-app-reg-assign-roles.png)

    
    **Schritt 3**: [Erstellen Sie eine Anmeldeinformationsentität](how-tos/credential-entity.md) in Metrics Advisor, damit Sie diese Entität auswählen können, wenn Sie einen Datenfeed für den Dienstprinzipal-Authentifizierungstyp hinzufügen. 
        
* **Dienstprinzipal aus Key Vault**: Key Vault hilft, kryptografische Schlüssel und Geheimniswerte zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Mit Key Vault können Sie Schlüssel und Geheimniswerte verschlüsseln. Erstellen Sie zuerst einen Dienstprinzipal, und speichern Sie den Dienstprinzipal dann in einem Schlüsseltresor. Weitere Informationen finden Sie unter [Erstellen einer Anmeldeinformationsentität für den Dienstprinzipal aus Key Vault](how-tos/credential-entity.md#sp-from-kv). 

* **Abfrage**: Legen Sie die Abfrage fest. Weitere Informationen finden Sie unter [Protokollabfragen in Azure Monitor](../../azure-monitor/logs/log-query-overview.md).

    Beispielabfrage:

    ``` Kusto
    [TableName]
    | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd)
    | summarize [count_per_dimension]=count() by [Dimension]
    ```

    Weitere Informationen finden Sie im [Tutorial zum Schreiben einer gültigen Abfrage](tutorials/write-a-valid-query.md).

## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL-Datenbank | SQL Server</span>

* **Verbindungszeichenfolge**: Die Authentifizierungstypen für Azure SQL-Datenbank und SQL Server sind Basic, verwaltete Identität, Azure SQL-Verbindungszeichenfolge, Dienstprinzipal und Dienstprinzipal aus Key Vault.
    
    * [Basic](/dotnet/framework/data/adonet/connection-string-syntax): Metrics Advisor akzeptiert eine **Verbindungszeichenfolge im ADO.NET-Stil** für eine SQL Server-Datenquelle.
    Hier ist ein Beispiel für eine Verbindungszeichenfolge: 
    
        ```
        Data Source=<Server>;Initial Catalog=<db-name>;User ID=<user-name>;Password=<password>
        ```
    
    * <span id='jump'>**Verwaltete Identität**</span>: Eine verwaltete Identität für Azure-Ressourcen kann den Zugriff auf Blob- und Warteschlangendaten autorisieren. Dazu verwendet die verwaltete Identität Azure AD-Anmeldeinformationen aus Anwendungen, die auf virtuellen Azure-Computern ausgeführt werden, sowie aus Funktions-Apps, VM-Skalierungsgruppen und anderen Diensten. Durch Verwendung einer verwalteten Identität für Azure-Ressourcen zusammen mit der Azure AD-Authentifizierung können Sie vermeiden, dass Anmeldeinformationen mit den in der Cloud ausgeführten Anwendungen gespeichert werden. Führen Sie folgende Schritte aus, um [Ihre verwaltete Identität zu aktivieren](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md):
    1. Eine systemseitig zugewiesene verwaltete Identität wird mit einem Mausklick aktiviert. Wechseln Sie im Azure-Portal für Ihren Metrics Advisor-Arbeitsbereich zu **Einstellungen** > **Identität** > **Vom System zugewiesen**. Legen Sie den Status auf **Ein** fest. 
    
        ![Screenshot: auf „Ein“ festgelegter Status](media/datafeeds/set-identity-status.png)

    1. Aktivieren Sie die Azure AD-Authentifizierung. Wechseln Sie im Azure-Portal für Ihre Datenquelle zu **Einstellungen** > **Active Directory-Administrator**. Wählen Sie **Administrator festlegen** und dann ein **Azure AD-Benutzerkonto** aus, das als Administrator des Servers festgelegt wird. Wählen Sie anschließend **Auswählen** aus.
    
        ![Screenshot: Festlegen des Administrators](media/datafeeds/set-admin.png)

    1. Aktivieren Sie die verwaltete Identität in Metrics Advisor. Sie können eine Abfrage im Datenbankverwaltungstool oder im Azure-Portal bearbeiten.
    
        **Verwaltungstool**: Wählen Sie im Datenbankverwaltungstool im Authentifizierungsfeld die Option **Active Directory: universell mit MFA-Unterstützung** aus. Geben Sie im Feld **Benutzername** den Namen des Azure AD-Kontos ein, das Sie in Schritt 2 als Serveradministrator festgelegt haben. Beispielsweise könnte dies `test@contoso.com` sein.
    
        ![Screenshot: Festlegen von Verbindungsdetails](media/datafeeds/connection-details.png)
        
        **Azure-Portal**: Wählen Sie in der SQL-Datenbank die Option **Abfrage-Editor** aus, und melden Sie sich beim Administratorkonto an.
        ![Screenshot: Bearbeiten von Abfragen im Azure-Portal](media/datafeeds/query-editor.png)

        Führen Sie dann im Abfragefenster Folgendes aus (beachten Sie, dass dies für die Verwaltungstoolmethode identisch ist):
    
        ```
        CREATE USER [MI Name] FROM EXTERNAL PROVIDER
        ALTER ROLE db_datareader ADD MEMBER [MI Name]
        ```
    
        > [!NOTE]
        > `MI Name` bezeichnet den Namen der verwalteten Identität in Metrics Advisor (für den Dienstprinzipal muss dieser durch den Dienstprinzipalnamen ersetzt werden). Weitere Informationen finden Sie unter [Autorisieren mit einer verwalteten Identität](../../storage/blobs/authorize-managed-identity.md#enable-managed-identities-on-a-vm). 
            
        Hier ist ein Beispiel für eine Verbindungszeichenfolge: 
       
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
        
    * **Azure SQL-Verbindungszeichenfolge**: 
      

        Hier ist ein Beispiel für eine Verbindungszeichenfolge: 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>;User ID=<user-name>;Password=<password>
        ```
  

    * **Dienstprinzipal**: Ein Dienstprinzipal ist eine konkrete Instanz, die aus dem Anwendungsobjekt erstellt wird und bestimmte Eigenschaften von diesem Anwendungsobjekt erbt. Ein Dienstprinzipal wird in jedem Mandanten erstellt, in dem die Anwendung verwendet wird, und verweist auf das global eindeutige Anwendungsobjekt. Das Dienstprinzipalobjekt definiert, was die App tatsächlich im jeweiligen Mandanten ausführen kann, wer auf die App zugreifen kann und auf welche Ressourcen die App zugreifen kann.
    
        **Schritt 1**: Erstellen und registrieren Sie eine Azure AD-Anwendung, und autorisieren Sie diese dann für den Zugriff auf eine Datenbank. Weitere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendungsregistrierung](/azure/data-explorer/provision-azure-ad-app).

        **Schritt 2**: Führen Sie die zuvor unter [Verwaltete Identität in SQL Server](#jump) dokumentierten Schritte aus. 

        **Schritt 3**: [Erstellen Sie eine Anmeldeinformationsentität](how-tos/credential-entity.md) in Metrics Advisor, damit Sie diese Entität auswählen können, wenn Sie einen Datenfeed für den Dienstprinzipal-Authentifizierungstyp hinzufügen. 

        Hier ist ein Beispiel für eine Verbindungszeichenfolge: 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
  
    * **Dienstprinzipal aus Key Vault**: Key Vault hilft, kryptografische Schlüssel und Geheimniswerte zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Mit Key Vault können Sie Schlüssel und Geheimniswerte verschlüsseln. Erstellen Sie zuerst einen Dienstprinzipal, und speichern Sie den Dienstprinzipal dann in einem Schlüsseltresor. Weitere Informationen finden Sie unter [Erstellen einer Anmeldeinformationsentität für den Dienstprinzipal aus Key Vault](how-tos/credential-entity.md#sp-from-kv). Sie finden die Verbindungszeichenfolge auch in Ihrer Azure SQL Server-Ressource unter **Einstellungen** > **Verbindungszeichenfolgen**.
        
        Hier ist ein Beispiel für eine Verbindungszeichenfolge: 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```

* **Abfrage**: Verwenden Sie eine SQL-Abfrage zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten. Sie können in der Abfrage `@IntervalStart` und `@IntervalEnd` verwenden, um einen erwarteten Metrikwert in einem Intervall abzurufen. Sie müssen wie folgt formatiert werden: `yyyy-MM-ddTHH:mm:ssZ`.


    Beispielabfrage:
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```
    
## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure Table Storage</span>

* **Verbindungszeichenfolge**: Erstellen Sie eine SAS-URL (Shared Access Signature), und geben Sie diese hier ein. Die einfachste Möglichkeit, eine SAS-URL zu generieren, ist die Verwendung des Azure-Portals. Navigieren Sie unter **Einstellungen** zu dem Speicherkonto, auf das Sie zugreifen möchten. Wählen Sie dann **Shared Access Signature** aus. Aktivieren Sie die Kontrollkästchen **Tabelle** und **Objekt**, und wählen Sie dann **SAS und Verbindungszeichenfolge generieren** aus. Kopieren Sie im Metrics Advisor-Arbeitsbereich die **SAS-URL für Tabellendienst**, und fügen Sie diese in das Textfeld ein.

    ![Screenshot: Generieren der Shared Access Signature in Azure Table Storage](media/azure-table-generate-sas.png)

* **Tabellenname**: Geben Sie eine Tabelle an, die abgefragt werden soll. Sie finden diese in Ihrer Azure Storage-Kontoinstanz. Klicken Sie im Abschnitt **Tabellendienst** auf **Tabellen**.

* **Abfrage**: Sie können in der Abfrage `@IntervalStart` und `@IntervalEnd` verwenden, um einen erwarteten Metrikwert in einem Intervall abzurufen. Sie müssen wie folgt formatiert werden: `yyyy-MM-ddTHH:mm:ssZ`.

    Beispielabfrage:
    
    ``` mssql
    PartitionKey ge '@IntervalStart' and PartitionKey lt '@IntervalEnd'
    ```

    Weitere Informationen finden Sie im [Tutorial zum Schreiben einer gültigen Abfrage](tutorials/write-a-valid-query.md).


## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **Verbindungszeichenfolge**: Die Verbindungszeichenfolge für den Zugriff auf InfluxDB.
* **Datenbank**: Die Datenbank, die abgefragt werden soll
* **Query** (Abfrage): Eine Abfrage zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten für die Erfassung

    Beispielabfrage:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    
Weitere Informationen finden Sie im [Tutorial zum Schreiben einer gültigen Abfrage](tutorials/write-a-valid-query.md).

* **Benutzername**: Dies ist für die Authentifizierung optional. 
* **Kennwort**: Dies ist für die Authentifizierung optional. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Verbindungszeichenfolge**: Die Verbindungszeichenfolge für den Zugriff auf MongoDB.
* **Datenbank**: Die Datenbank, die abgefragt werden soll
* **Abfrage**: Ein Befehl zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten für die Erfassung. Überprüfen Sie den Befehl für [db.runCommand()](https://docs.mongodb.com/manual/reference/method/db.runCommand/index.html).

    Beispielabfrage:

    ``` MongoDB
    {"find": "[TableName]","filter": { [Timestamp]: { $gte: ISODate(@IntervalStart) , $lt: ISODate(@IntervalEnd) }},"singleBatch": true}
    ```
    

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Verbindungszeichenfolge**: Die Verbindungszeichenfolge für den Zugriff auf MySQL DB.
* **Query** (Abfrage): Eine Abfrage zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten für die Erfassung

    Beispielabfrage:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn]< @IntervalEnd
    ```

    Weitere Informationen finden Sie im [Tutorial zum Schreiben einer gültigen Abfrage](tutorials/write-a-valid-query.md).

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Verbindungszeichenfolge**: Die Verbindungszeichenfolge für den Zugriff auf PostgreSQL DB.
* **Query** (Abfrage): Eine Abfrage zum Abrufen und Formulieren von Daten in mehrdimensionalen Zeitreihendaten für die Erfassung

    Beispielabfrage:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    Weitere Informationen finden Sie im [Tutorial zum Schreiben einer gültigen Abfrage](tutorials/write-a-valid-query.md).
    
## <a name="span-idcsvlocal-files-csvspan"></a><span id="csv">Lokale Dateien (CSV)</span>

> [!NOTE]
> Dieses Feature wird nur für die schnelle Systemauswertung verwendet, die sich auf die Anomalieerkennung konzentriert. Es akzeptiert nur statische Daten aus einer lokalen CSV-Datei und führt eine Anomalieerkennung für einzelne Zeitreihendaten durch. Verwenden Sie für die Analyse mehrdimensionaler Metriken, einschließlich Echtzeit-Datenerfassung, Anomaliebenachrichtigung, Ursachenanalyse und metrikübergreifender Incidentanalyse, andere unterstützte Datenquellen.

**Anforderungen an Daten in CSV-Dateien:**
- Sie benötigen mindestens eine Spalte, die die zu analysierenden Messungen darstellt. Probieren Sie für eine bessere und schnellere Nutzung eine CSV-Datei aus, die zwei Spalten enthält: eine Zeitstempelspalte und eine Metrikspalte. Das Zeitstempelformat sollte wie folgt aussehen: `2021-03-30T00:00:00Z`. Der `seconds`-Teil lautet am besten `:00Z`. Die Zeitgranularität zwischen den einzelnen Datensätzen sollte identisch sein.
- Die Zeitstempelspalte ist optional. Wenn kein Zeitstempel vorhanden ist, wird von Metrics Advisor der Zeitstempel ab heute verwendet (`00:00:00` – koordinierte Weltzeit). Der Dienst ordnet jedes Measure in der Zeile in einem Intervall von einer Stunde zu.
- Während der Datenerfassung findet keine Neusortierung statt, und es werden keine Lücken ausgefüllt. Vergewissern Sie sich, dass die Daten in der CSV-Datei nach der Zeitstempelreihenfolge **aufsteigend (ASC)** geordnet sind.
 
## <a name="next-steps"></a>Nächste Schritte

* Während Sie darauf warten, dass die Metrikdaten im System erfasst werden, lesen Sie mehr über das [Verwalten von Datenfeedkonfigurationen](how-tos/manage-data-feeds.md).
* Wenn die Metrikdaten erfasst wurden, können Sie [Metriken konfigurieren und die Erkennungskonfiguration optimieren](how-tos/configure-metrics.md).
---
title: Benannte Entitäten für personenbezogene Informationen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 09/09/2021
ms.author: aahi
ms.openlocfilehash: ce85895307af9e437fb9ceaaff3f1a385e77e17d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672566"
---
> [!NOTE]
> Um geschützte Gesundheitsinformationen (PHI) zu ermitteln, verwenden Sie den `domain=phi`-Parameter und die Modellversion `2020-04-01` oder höher.
>
> Beispiel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
Die folgenden Entitätskategorien werden zurückgegeben, wenn Sie Anforderungen an den `/v3.1/entities/recognition/pii`-Endpunkt senden.


| Category   |  BESCHREIBUNG                          |
|------------|-------------|
| [Person](#category-person)      |  Namen von Personen  |
| [PersonType](#category-persontype) | Von einer Person eingenommene Position oder Rolle |
| [Telefonnummer](#category-phonenumber) |Telefonnummern (nur US- und EU-Telefonnummern) |
| [Organisation](#category-organization) |  Firmen, Gruppen, Regierungsstellen und andere Organisationen  |
| [Adresse](#category-address) | Vollständige Postanschrift  |
| [E-Mail](#category-email) | E-Mail-Adressen   |
| [URL](#category-url) | URLs zu Websites  |
| [IPAddress](#category-ipaddress) | Netzwerk-IP-Adressen  |
| [DateTime](#category-datetime) | Datums- und Uhrzeitangaben | 
| [Menge](#category-quantity) | Zahlen und numerische Mengen.  |
| [Azure-Informationen](#azure-information) | Identifizierbare Azure-Informationen (beispielsweise Authentifizierungsinformationen)  |
| [Identifikation](#identification) | Finanz- und landesspezifische Identifikation  |

### <a name="category-person"></a>Kategorie: Person

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Person

    :::column-end:::
    :::column span="2":::
        **Details**

        Namen von Personen Wird auch mit `domain=phi`zurückgegeben

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `Person` hinzu. In der API-Antwort wird `Person` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Kategorie: PersonType

Diese Kategorie enthält die folgende Entität:


:::row:::
    :::column span="":::
        **Entität**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Details**

        Von einer Person eingenommene Position oder Rolle

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `PersonType` hinzu. In der API-Antwort wird `PersonType` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Kategorie: PhoneNumber

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Details**

        Telefonnummern (nur US- und EU-Telefonnummern) Wird auch mit `domain=phi`zurückgegeben

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `PhoneNumber` hinzu. In der API-Antwort wird `PhoneNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::

:::row-end:::


### <a name="category-organization"></a>Kategorie: Organization

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Organization

    :::column-end:::
    :::column span="2":::
        **Details**

        Firmen, politische Gruppen, Musikgruppen, Sportvereine, Regierungsstellen und öffentliche Organisationen Nationalitäten und Religionen werden in diesem Entitätstyp nicht berücksichtigt. Wird auch mit `domain=phi`zurückgegeben

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `Organization` hinzu. In der API-Antwort wird `Organization` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::

:::row-end:::

### <a name="category-address"></a>Kategorie: Adresse

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Adresse

    :::column-end:::
    :::column span="2":::
        **Details**

        vollständige Postanschrift Wird auch mit `domain=phi`zurückgegeben

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `Address` hinzu. In der API-Antwort wird `Address` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-email"></a>Kategorie: Email

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Email

    :::column-end:::
    :::column span="2":::
        **Details**

        E-Mail-Adressen Wird auch mit `domain=phi`zurückgegeben
      
        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `Email` hinzu. In der API-Antwort wird `Email` zurückgegeben, falls es erkannt wird.

    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::


### <a name="category-url"></a>Kategorie: URL

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        URL

    :::column-end:::
    :::column span="2":::
        **Details**

        URLs zu Websites Wird auch mit `domain=phi`zurückgegeben

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `URL` hinzu. In der API-Antwort wird `URL` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

### <a name="category-ipaddress"></a>Kategorie: IPAddress

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        IPAddress

    :::column-end:::
    :::column span="2":::
        **Details**

        Netzwerk-IP-Adressen Wird auch mit `domain=phi`zurückgegeben

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `IPAddress` hinzu. In der API-Antwort wird `IPAddress` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Kategorie: Datetime

Diese Kategorie enthält die folgenden Entitäten:

:::row:::
    :::column span="":::
        **Entität**

        Datetime

    :::column-end:::
    :::column span="2":::
        **Details**

        Datums- und Uhrzeitangaben 

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `DateTime` hinzu. In der API-Antwort wird `DateTime` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
:::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Kategorie: Menge

Diese Kategorie enthält die folgenden Entitäten:

:::row:::
    :::column span="":::
        **Entität**

        Menge

    :::column-end:::
    :::column span="2":::
        **Details**

        Zahlen und numerische Mengen.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `Quantity` hinzu. In der API-Antwort wird `Quantity` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Unterkategorien

Die Entität in dieser Kategorie kann die folgenden Unterkategorien aufweisen.

:::row:::
    :::column span="":::
        **Entitätsunterkategorie**

        Age

    :::column-end:::
    :::column span="2":::
        **Details**

        Altersangaben 

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `Age` hinzu. In der API-Antwort wird `Age` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="2":::
        **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Azure-Informationen

Diese Entitätskategorien beinhalten identifizierbare Azure-Informationen (einschließlich Authentifizierungsinformationen und Verbindungszeichenfolgen). Wird nicht mit dem `domain=phi`-Parameter zurückgegeben

:::row:::
    :::column span="":::
        **Entität**

        Azure DocumentDB-Authentifizierungsschlüssel 

    :::column-end:::
    :::column span="2":::
        **Details**

        Autorisierungsschlüssel für einen Azure Cosmos DB-Server.   

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `AzureDocumentDBAuthKey` hinzu. In der API-Antwort wird `AzureDocumentDBAuthKey` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Verbindungszeichenfolge für Azure-IaaS-Datenbank und Azure SQL
        

    :::column-end:::
    :::column span="2":::

        Verbindungszeichenfolge für eine Azure-IaaS-Datenbank (Infrastructure-as-a-Service) und Azure SQL

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `AzureIAASDatabaseConnectionAndSQLString` hinzu. In der API-Antwort wird `AzureIAASDatabaseConnectionAndSQLString` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure IoT-Verbindungszeichenfolge  

    :::column-end:::
    :::column span="2":::

        Verbindungszeichenfolge für Azure IoT. 
      
        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `AzureIoTConnectionString` hinzu. In der API-Antwort wird `AzureIoTConnectionString` zurückgegeben, falls es erkannt wird.

    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure-Veröffentlichungseinstellung: Kennwort  

    :::column-end:::
    :::column span="2":::

        Kennwort für die Azure-Veröffentlichungseinstellungen.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `AzurePublishSettingPassword` hinzu. In der API-Antwort wird `AzurePublishSettingPassword` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Redis Cache-Verbindungszeichenfolge 

    :::column-end:::
    :::column span="2":::

        Verbindungszeichenfolge für einen Redis-Cache.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `AzureRedisCacheString` hinzu. In der API-Antwort wird `AzureRedisCacheString` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SaaS 

    :::column-end:::
    :::column span="2":::

        Verbindungszeichenfolge für Azure SaaS (Software-as-a-Service).

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `AzureSAS` hinzu. In der API-Antwort wird `AzureSAS` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Service Bus-Verbindungszeichenfolge

    :::column-end:::
    :::column span="2":::

        Verbindungszeichenfolge für eine Azure Service Bus-Instanz.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `AzureServiceBusString` hinzu. In der API-Antwort wird `AzureServiceBusString` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage-Kontoschlüssel 

    :::column-end:::
    :::column span="2":::

        Schlüssel für ein Azure-Speicherkonto. 

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `AzureStorageAccountKey` hinzu. In der API-Antwort wird `AzureStorageAccountKey` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure Storage-Kontoschlüssel (generisch)

    :::column-end:::
    :::column span="2":::

        Generischer Schlüssel für ein Azure-Speicherkonto.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `AzureStorageAccountGeneric` hinzu. In der API-Antwort wird `AzureStorageAccountGeneric` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        SQL Server-Verbindungszeichenfolge 

    :::column-end:::
    :::column span="2":::

        Verbindungszeichenfolge für einen Computer mit SQL Server.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `piiCategories`-Parameter `SQLServerConnectionString` hinzu. In der API-Antwort wird `SQLServerConnectionString` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identifikation

[!INCLUDE [supported identification entities](./identification-entities.md)]

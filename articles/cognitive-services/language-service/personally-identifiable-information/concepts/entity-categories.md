---
title: Entitätskategorien, die von personenbezogenen Informationen (Erkennung) in Azure Cognitive Service für Sprache erkannt werden
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über die Entitäten, die das PII-Feature aus unstrukturiertem Text erkennen kann.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/15/2021
ms.author: aahi
ms.custom: language-service-pii, ignite-fall-2021
ms.openlocfilehash: 099926359944c1f9e9a673f3c754019c761e6220
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519532"
---
# <a name="supported-personally-identifiable-information-pii-entity-categories"></a>Unterstützte Entitätskategorien für personenbezogene Informationen (Personally Identifiable Information, PII)

In diesem Artikel finden Sie die Entitätskategorien, die von der [PII-Erkennungsfunktion](../how-to-call.md) zurückgegeben werden können. Dieses Feature führt ein Vorhersagemodell aus, um vertrauliche Informationen aus einem Eingabedokument zu identifizieren, zu kategorisieren und zu redigieren.

Das Merkmal PII umfasst die Fähigkeit, persönliche (`PII`) und gesundheitsrelevante (`PHI`) Informationen zu erkennen.

## <a name="entity-categories"></a>Entitätskategorien

> [!NOTE]
> Um geschützte Gesundheitsinformationen (PHI) zu ermitteln, verwenden Sie den `domain=phi`-Parameter und die Modellversion `2020-04-01` oder höher.
>
> Beispiel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
Die folgenden Entitätskategorien werden zurückgegeben, wenn Sie das PII-Feature für API-Anforderungen senden.

## <a name="category-person"></a>Kategorie: Person

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Person

    :::column-end:::
    :::column span="2":::
        **Details**

        Namen von Personen Werden sowohl als PII als auch als PHI zurückgegeben.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `Person` hinzu. In der API-Antwort wird `Person` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::

## <a name="category-persontype"></a>Kategorie: PersonType

Diese Kategorie enthält die folgende Entität:


:::row:::
    :::column span="":::
        **Entität**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Details**

        Von einer Person eingenommene Position oder Rolle

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `PersonType` hinzu. In der API-Antwort wird `PersonType` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

## <a name="category-phonenumber"></a>Kategorie: PhoneNumber

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Details**

        Telefonnummern (nur US- und EU-Telefonnummern) Werden sowohl als PII als auch als PHI zurückgegeben.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `PhoneNumber` hinzu. In der API-Antwort wird `PhoneNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
   :::column-end:::

:::row-end:::


## <a name="category-organization"></a>Kategorie: Organization

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Organization

    :::column-end:::
    :::column span="2":::
        **Details**

        Firmen, politische Gruppen, Musikgruppen, Sportvereine, Regierungsstellen und öffentliche Organisationen Nationalitäten und Religionen werden in diesem Entitätstyp nicht berücksichtigt. Werden sowohl als PII als auch als PHI zurückgegeben.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `Organization` hinzu. In der API-Antwort wird `Organization` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::

:::row-end:::

#### <a name="subcategories"></a>Unterkategorien

Die Entität in dieser Kategorie kann die folgenden Unterkategorien aufweisen.

:::row:::
    :::column span="":::
        **Entitätsunterkategorie**

        Medizin    

    :::column-end:::
    :::column span="2":::
        **Details**

        Medizinische Unternehmen und Gruppen

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `OrganizationMedical` hinzu. In der API-Antwort wird `OrganizationMedical` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Börse

    :::column-end:::
    :::column span="2":::

        Börsengruppen 

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `OrganizationStockExchange` hinzu. In der API-Antwort wird `OrganizationStockExchange` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Sportbezogene Organisationen

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `OrganizationSports` hinzu. In der API-Antwort wird `OrganizationSports` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::

      `en`   
      
   :::column-end:::

:::row-end:::


## <a name="category-address"></a>Kategorie: Adresse

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Adresse

    :::column-end:::
    :::column span="2":::
        **Details**

        vollständige Postanschrift Werden sowohl als PII als auch als PHI zurückgegeben.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `Address` hinzu. In der API-Antwort wird `Address` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

## <a name="category-email"></a>Kategorie: Email

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Email

    :::column-end:::
    :::column span="2":::
        **Details**

        E-Mail-Adressen Werden sowohl als PII als auch als PHI zurückgegeben.
      
        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `Email` hinzu. In der API-Antwort wird `Email` zurückgegeben, falls es erkannt wird.

    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::


## <a name="category-url"></a>Kategorie: URL

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        URL

    :::column-end:::
    :::column span="2":::
        **Details**

        URLs zu Websites Werden sowohl als PII als auch als PHI zurückgegeben.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `URL` hinzu. In der API-Antwort wird `URL` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::

:::row-end:::

## <a name="category-ip"></a>Kategorie: IP

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        IP

    :::column-end:::
    :::column span="2":::
        **Details**

        Netzwerk-IP-Adressen Werden sowohl als PII als auch als PHI zurückgegeben.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `IP` hinzu. In der API-Antwort wird `IP` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::

    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
    :::column-end:::
:::row-end:::

## <a name="category-datetime"></a>Kategorie: Datetime

Diese Kategorie enthält die folgenden Entitäten:

:::row:::
    :::column span="":::
        **Entität**

        Datetime

    :::column-end:::
    :::column span="2":::
        **Details**

        Datums- und Uhrzeitangaben 

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `DateTime` hinzu. In der API-Antwort wird `DateTime` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
:::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Unterkategorien

Die Entität in dieser Kategorie kann die folgenden Unterkategorien aufweisen.

:::row:::
    :::column span="":::
        **Entitätsunterkategorie**

        Date

    :::column-end:::
    :::column span="2":::
        **Details**

        Kalenderdatumsangaben Werden sowohl als PII als auch als PHI zurückgegeben.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `Date` hinzu. In der API-Antwort wird `Date` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**
      
      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
    :::column-end:::
:::row-end:::

## <a name="category-quantity"></a>Kategorie: Menge

Diese Kategorie enthält die folgenden Entitäten:

:::row:::
    :::column span="":::
        **Entität**

        Menge

    :::column-end:::
    :::column span="2":::
        **Details**

        Zahlen und numerische Mengen.

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `Quantity` hinzu. In der API-Antwort wird `Quantity` zurückgegeben, falls es erkannt wird.
      
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

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `Age` hinzu. In der API-Antwort wird `Age` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="2":::
        **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Azure-Informationen

Diese Entitätskategorien beinhalten identifizierbare Azure-Informationen (einschließlich Authentifizierungsinformationen und Verbindungszeichenfolgen). Werden nicht als PHI zurückgegeben.

:::row:::
    :::column span="":::
        **Entität**

        Azure DocumentDB-Authentifizierungsschlüssel 

    :::column-end:::
    :::column span="2":::
        **Details**

        Autorisierungsschlüssel für einen Azure Cosmos DB-Server.   

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AzureDocumentDBAuthKey` hinzu. In der API-Antwort wird `AzureDocumentDBAuthKey` zurückgegeben, falls es erkannt wird.
      
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

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AzureIAASDatabaseConnectionAndSQLString` hinzu. In der API-Antwort wird `AzureIAASDatabaseConnectionAndSQLString` zurückgegeben, falls es erkannt wird.
      
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
      
        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AzureIoTConnectionString` hinzu. In der API-Antwort wird `AzureIoTConnectionString` zurückgegeben, falls es erkannt wird.

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

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AzurePublishSettingPassword` hinzu. In der API-Antwort wird `AzurePublishSettingPassword` zurückgegeben, falls es erkannt wird.
      
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

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AzureRedisCacheString` hinzu. In der API-Antwort wird `AzureRedisCacheString` zurückgegeben, falls es erkannt wird.
      
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

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AzureSAS` hinzu. In der API-Antwort wird `AzureSAS` zurückgegeben, falls es erkannt wird.
      
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

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AzureServiceBusString` hinzu. In der API-Antwort wird `AzureServiceBusString` zurückgegeben, falls es erkannt wird.
      
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

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AzureStorageAccountKey` hinzu. In der API-Antwort wird `AzureStorageAccountKey` zurückgegeben, falls es erkannt wird.
      
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

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AzureStorageAccountGeneric` hinzu. In der API-Antwort wird `AzureStorageAccountGeneric` zurückgegeben, falls es erkannt wird.
      
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

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `SQLServerConnectionString` hinzu. In der API-Antwort wird `SQLServerConnectionString` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en` 

    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identifikation

[!INCLUDE [supported identification entities](../includes/identification-entities.md)]


## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über NER](../overview.md)

---
title: Identifikationsentitäten
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-pii, ignite-fall-2021
ms.openlocfilehash: 24b9e8163f6c3aef14f6e17e45fe59da1fb49e29
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094962"
---
## <a name="financial-account-identification"></a>Identifikation bei Finanzkonten

Diese Entitätskategorie umfasst Finanzinformationen und offizielle Formen der Identifikation.

## <a name="category-aba-routing-number"></a>Kategorie: ABA-Nummer

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        ABA-Nummer

    :::column-end:::
    :::column span="2":::
        **Details**

        ABA-Nummern (American Bankers Association) für den Zahlungsverkehr mit US-amerikanischen Banken. Wird auch mit `domain=phi`zurückgegeben

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ABARoutingNumber` hinzu. Auch in der API-Antwort wird `ABARoutingNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::


## <a name="category-swift-code"></a>Kategorie: SWIFT-Code

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        SWIFT-Code

    :::column-end:::
    :::column span="2":::
        **Details**

        SWIFT-Codes in Zahlungsanweisungen. Wird auch mit `domain=phi`zurückgegeben

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `SWIFTCode` hinzu. In der API-Antwort wird `SWIFTCode` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
   :::column-end:::
:::row-end:::

## <a name="category-credit-card"></a>Kategorie: Kreditkarte

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Kreditkarte

    :::column-end:::
    :::column span="2":::
        **Details**

        Kreditkartennummern Wird auch mit `domain=phi`zurückgegeben

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `CreditCardNumber` hinzu. In der API-Antwort wird `CreditCardNumber` zurückgegeben, falls es erkannt wird.

    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
   :::column-end:::
:::row-end:::

## <a name="category-international-banking-account-number-iban"></a>Kategorie: IBAN (International Banking Account Number) 

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Kreditkarte

    :::column-end:::
    :::column span="2":::
        **Details**

        IBAN-Codes in Zahlungsanweisungen. Wird auch mit `domain=phi`zurückgegeben

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `InternationlBankingAccountNumber` hinzu. In der API-Antwort wird `InternationlBankingAccountNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

## <a name="government-and-countryregion-specific-identification"></a>Behördliche und landes-/regionsspezifische Informationen zur Identifikation

> [!NOTE]
> Die folgenden finanziellen und länderspezifischen Entitäten werden mit dem Parameter `domain=phi` nicht zurückgegeben:
> * Reisepassnummern
> * Steuernummern

Die folgenden Entitäten sind nach Land/Region gruppiert und aufgelistet:

### <a name="argentina"></a>Argentinien

:::row:::
    :::column span="":::
        **Entität**

        Nationale argentinische ID-Nummer (DNI) 

    :::column-end:::
    :::column span="2":::
        **Details** (Auch mit `domain=phi` zurückgegeben.)
        
        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ARNationalIdentityNumber` hinzu. In der API-Antwort wird `ARNationalIdentityNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


### <a name="austria"></a>Österreich

:::row:::
    :::column span="":::
        **Entität**

        Österreichischer Personalausweis

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ATIdentityCard` hinzu. In der API-Antwort wird `ATIdentityCard` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Österreichische Abgabenkontonummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ATTaxIdentificationNumber` hinzu. In der API-Antwort wird `ATTaxIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Österreichische Umsatzsteuer-Identifikationsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ATValueAddedTaxNumber` hinzu. In der API-Antwort wird `ATValueAddedTaxNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



### <a name="australia"></a>Australien

:::row:::
    :::column span="":::
        **Entität**

        Australische Bankkontonummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AUDriversLicenseNumber` hinzu. In der API-Antwort wird `AUDriversLicenseNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australische Geschäftsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AUBusinessNumber` hinzu. In der API-Antwort wird `AUBusinessNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australische Unternehmensnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AUCompanyNumber` hinzu. In der API-Antwort wird `AUCompanyNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australischer Führerschein  

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AUDriversLicense` hinzu. In der API-Antwort wird `AUDriversLicense` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australische Krankenversicherungsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `AUMedicalAccountNumber` hinzu. In der API-Antwort wird `AUMedicalAccountNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australische Reisepassnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ATPassportNumber` hinzu. In der API-Antwort wird `ATPassportNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Australische Steuernummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ATTaxIdentificationNumber` hinzu. In der API-Antwort wird `ATTaxIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


### <a name="belgium"></a>Belgien

:::row:::
    :::column span="":::
        **Entität**

        Belgische Nationalregisternummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `BENationalNumber` hinzu. In der API-Antwort wird `BENationalNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Belgische Umsatzsteuer-Identifikationsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `BEValueAddedTaxNumber` hinzu. In der API-Antwort wird `BEValueAddedTaxNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


### <a name="brazil"></a>Brasilien 

:::row:::
    :::column span="":::
        **Entität**

        Brasilianische Nummer juristischer Personen (CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `BRLegalEntityNumber` hinzu. In der API-Antwort wird `BRLegalEntityNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Brasilianische CPF-Nummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `BRCPFNumber` hinzu. In der API-Antwort wird `BRCPFNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nationale brasilianische ID-Karte (RG)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `BRNationalIDRG` hinzu. In der API-Antwort wird `BRNationalIDRG` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

### <a name="canada"></a>Canada

:::row:::
    :::column span="":::
        **Entität**

        Kanadische Bankkontonummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `CABankAccountNumber` hinzu. In der API-Antwort wird `CABankAccountNumber` zurückgegeben, falls es erkannt wird.
    
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanadische Führerscheinnummer

    :::column-end:::

    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `CADriversLicenseNumber` hinzu. In der API-Antwort wird `CADriversLicenseNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Nummer des kanadischen Gesundheitsdiensts

        
    :::column-end:::

    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `CAHealthServiceNumber` hinzu. In der API-Antwort wird `CAHealthServiceNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanadische Reisepassnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `CAPassportNumber` hinzu. In der API-Antwort wird `CAPassportNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Persönliche kanadische Krankenversicherungsnummer (PHIN, Personal Health Identification Number)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `CAPersonalHealthIdentification` hinzu. In der API-Antwort wird `CAPersonalHealthIdentification` zurückgegeben, falls es erkannt wird.

        Wird auch mit `domain=phi`zurückgegeben
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Kanadische Sozialversicherungsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `CASocialInsuranceNumber` hinzu. In der API-Antwort wird `CASocialInsuranceNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

### <a name="chile"></a>Chile 

:::row:::
    :::column span="":::
        **Entität**

        Chilenische Personalausweisnummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `CLIdentityCardNumber` hinzu. In der API-Antwort wird `CLIdentityCardNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `es`
      
   :::column-end:::
:::row-end:::

### <a name="china"></a>China

:::row:::
    :::column span="":::
        **Entität**

        Personalausweisnummer für Personen mit Wohnsitz in China (VRC)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `CNResidentIdentityCardNumber` hinzu. In der API-Antwort wird `CNResidentIdentityCardNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


### <a name="european-union-eu"></a>Europäische Union (EU)

:::row:::
    :::column span="":::
        **Entität**

        EU-Debitkartennummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `EUDebitCardNumber` hinzu. In der API-Antwort wird `EUDebitCardNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU-Führerscheinnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `EUDriversLicenseNumber` hinzu. In der API-Antwort wird `EUDriversLicenseNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU-GPU-Koordinaten

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `EUGPSCoordinates` hinzu. In der API-Antwort wird `EUGPSCoordinates` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU-Personalausweisnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `EUNationalIdentificationNumber` hinzu. In der API-Antwort wird `EUNationalIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU-Reisepassnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `EUPassportNumber` hinzu. In der API-Antwort wird `EUPassportNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU-Sozialversicherungsnummer (SSN) oder entsprechende ID

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `EUSocialSecurityNumber` hinzu. In der API-Antwort wird `EUSocialSecurityNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        EU-Steueridentifikationsnummer (TIN)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `EUTaxIdentificationNumber` hinzu. In der API-Antwort wird `EUTaxIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

### <a name="france"></a>Frankreich

:::row:::
    :::column span="":::
        **Entität**

        Französische Führerscheinnummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `FRDriversLicenseNumber` hinzu. In der API-Antwort wird `FRDriversLicenseNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Französische Krankenversicherungsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `FRHealthInsuranceNumber` hinzu. In der API-Antwort wird `FRHealthInsuranceNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Französischer Personalausweis (Carte Nationale d'Identité, CNI)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `FRNationalID` hinzu. In der API-Antwort wird `FRNationalID` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Französische Reisepassnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `FRPassportNumber` hinzu. In der API-Antwort wird `FRPassportNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Französische Sozialversicherungsnummer (NIRPP)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `FRSocialSecurityNumber` hinzu. In der API-Antwort wird `FRSocialSecurityNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Französische Steueridentifikationsnummer (SPI)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `FRTaxIdentificationNumber` hinzu. In der API-Antwort wird `FRTaxIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Französische Umsatzsteuer-Identifikationsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `FRValueAddedTaxNumber` hinzu. In der API-Antwort wird `FRValueAddedTaxNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

### <a name="germany"></a>Deutschland

:::row:::
    :::column span="":::
        **Entität**

        Deutsche Führerscheinnummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `DEDriversLicenseNumber` hinzu. In der API-Antwort wird `DEDriversLicenseNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Deutsche Personalausweisnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `DEIdentityCardNumber` hinzu. In der API-Antwort wird `DEIdentityCardNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Deutsche Reisepassnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `DEPassportNumber` hinzu. In der API-Antwort wird `DEPassportNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Deutsche Steueridentifikationsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `DETaxIdentificationNumber` hinzu. In der API-Antwort wird `DETaxIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Deutsche Umsatzsteuer-Identifikationsnummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `DEValueAddedNumber` hinzu. In der API-Antwort wird `DEValueAddedNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

### <a name="hong-kong"></a>Hongkong

:::row:::
    :::column span="":::
        **Entität**

        ID-Kartennummer aus Hongkong (HKID)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `HKIdentityCardNumber` hinzu. In der API-Antwort wird `HKIdentityCardNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::

### <a name="hungary"></a>Ungarn

:::row:::
    :::column span="":::
        **Entität**

        Ungarische persönliche ID-Nummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `HUPersonalIdentificationNumber` hinzu. In der API-Antwort wird `HUPersonalIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ungarische Steueridentifikationsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `HUTaxIdentificationNumber` hinzu. In der API-Antwort wird `HUTaxIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ungarische Umsatzsteuer-Identifikationsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `HUValueAddedNumber` hinzu. In der API-Antwort wird `HUValueAddedNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

### <a name="india"></a>Indien

:::row:::
    :::column span="":::
        **Entität**

        Indische PAN (Permanent Account Number)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `INPermanentAccount` hinzu. In der API-Antwort wird `INPermanentAccount` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Indische eindeutige ID-Nummer (Aadhaar)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `INUniqueIdentificationNumber` hinzu. In der API-Antwort wird `INUniqueIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


### <a name="indonesia"></a>Indonesien

:::row:::
    :::column span="":::
        **Entität**

        Indonesische ID-Kartennummer (KTP)

    :::column-end:::
    :::column span="2":::

        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `IDIdentityCardNumber` hinzu. In der API-Antwort wird `IDIdentityCardNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::

### <a name="ireland"></a>Irland

:::row:::
    :::column span="":::
        **Entität**

        Irische Sozialversicherungsnummer (PPS, Personal Public Service)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `IEPersonalPublicServiceNumber` hinzu. In der API-Antwort wird `IEPersonalPublicServiceNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        Irische Sozialversicherungsnummer (Personal Public Service, PPS), Version 2

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `IEPersonalPublicServiceNumberV2` hinzu. In der API-Antwort wird `IEPersonalPublicServiceNumberV2` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::

### <a name="israel"></a>Israel

:::row:::
    :::column span="":::
        **Entität**

        Nationale israelische ID-Nummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ILNationalID` hinzu. In der API-Antwort wird `ILNationalID` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Israelische Bankkontonummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ILBankAccountNumber` hinzu. In der API-Antwort wird `ILBankAccountNumber` zurückgegeben, falls es erkannt wird.
    
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

### <a name="italy"></a>Italien

:::row:::
    :::column span="":::
        **Entität**

        Italienische Führerscheinnummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ITDriversLicenseNumber` hinzu. In der API-Antwort wird `ITDriversLicenseNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Italienische Steuernummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ITFiscalCode` hinzu. In der API-Antwort wird `ITFiscalCode` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Italienische Umsatzsteuer-Identifikationsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ITValueAddedTaxNumber` hinzu. In der API-Antwort wird `ITValueAddedTaxNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


### <a name="japan"></a>Japan

:::row:::
    :::column span="":::
        **Entität**

        Japanische Bankkontonummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `JPBankAccountNumber` hinzu. In der API-Antwort wird `JPBankAccountNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japanische Führerscheinnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `JPDriversLicenseNumber` hinzu. In der API-Antwort wird `JPDriversLicenseNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japanische „Meine Nummer“ (persönlich)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `JPMyNumberPersonal` hinzu. In der API-Antwort wird `JPMyNumberPersonal` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japanische „Meine Nummer“ (geschäftlich)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `JPMyNumberCorporate` hinzu. In der API-Antwort wird `JPMyNumberCorporate` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japanische Melderegisternummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ITValueAddedTaxNumber` hinzu. In der API-Antwort wird `ITValueAddedTaxNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japanische Personalausweisnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `JPResidenceCardNumber` hinzu. In der API-Antwort wird `JPResidenceCardNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japanische Sozialversicherungsnummer (SIN)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `JPSocialInsuranceNumber` hinzu. In der API-Antwort wird `JPSocialInsuranceNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Japanische Reisepassnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `JPPassportNumber` hinzu. In der API-Antwort wird `JPPassportNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

### <a name="luxembourg"></a>Luxemburg

:::row:::
    :::column span="":::
        **Entität**

        Luxemburgische nationale Identifikationsnummer (natürliche Personen)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `LUNationalIdentificationNumberNatural` hinzu. In der API-Antwort wird `LUNationalIdentificationNumberNatural` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Luxemburgische nationale Identifikationsnummer (juristische Personen)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `LUNationalIdentificationNumberNonNatural` hinzu. In der API-Antwort wird `LUNationalIdentificationNumberNonNatural` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entität**

        Maltesische ID-Kartennummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `MTIdentityCardNumber` hinzu. In der API-Antwort wird `MTIdentityCardNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Maltesische Steueridentifikationsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `MTTaxIDNumber` hinzu. In der API-Antwort wird `MTTaxIDNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


### <a name="new-zealand"></a>Neuseeland

:::row:::
    :::column span="":::
        **Entität**

        Neuseeländische Bankkontonummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `NZBankAccountNumber` hinzu. In der API-Antwort wird `NZBankAccountNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Neuseeländische Führerscheinnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `NZDriversLicenseNumber` hinzu. In der API-Antwort wird `NZDriversLicenseNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Neuseeländische Steuernummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `NZInlandRevenueNumber` hinzu. In der API-Antwort wird `NZInlandRevenueNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Neuseeländische Nummer des Gesundheitsministeriums

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `NZMinistryOfHealthNumber` hinzu. In der API-Antwort wird `NZMinistryOfHealthNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Neuseeländische Sozialversicherungsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `NZSocialWelfareNumber` hinzu. In der API-Antwort wird `NZSocialWelfareNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


### <a name="philippines"></a>Philippinen

:::row:::
    :::column span="":::
        **Entität**

        Philippinische einheitliche Mehrzweck-ID-Nummer (Unified Multi-Purpose ID)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `PHUnifiedMultiPurposeIDNumber` hinzu. In der API-Antwort wird `PHUnifiedMultiPurposeIDNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::

### <a name="portugal"></a>Portugal 

:::row:::
    :::column span="":::
        **Entität**

        Portugiesische ID-Kartennummer (Citizen Card)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `PTCitizenCardNumber` hinzu. In der API-Antwort wird `PTCitizenCardNumber` zurückgegeben, falls es erkannt wird.
          
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Portugiesische Steueridentifikationsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `PTTaxIdentificationNumber` hinzu. In der API-Antwort wird `PTTaxIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

### <a name="singapore"></a>Singapur

:::row:::
    :::column span="":::
        **Entität**

        Singapur: Nationale Registrierungs-ID-Kartennummer (NRIC)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `PTTaxIdentificationNumber` hinzu. In der API-Antwort wird `PTTaxIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


### <a name="south-africa"></a>Südafrika

:::row:::
    :::column span="":::
        **Entität**

        Südafrikanische ID-Nummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ZAIdentificationNumber` hinzu. In der API-Antwort wird `ZAIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::


### <a name="south-korea"></a>Südkorea

:::row:::
    :::column span="":::
        **Entität**

        Südkoreanische Einwohnerregistrierungsnummer (Resident Registration Number)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `KRResidentRegistrationNumber` hinzu. In der API-Antwort wird `KRResidentRegistrationNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `ko`
      
   :::column-end:::
:::row-end:::

### <a name="spain"></a>Spanien

:::row:::
    :::column span="":::
        **Entität**

        Spanischer Personalausweis (DNI)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ESDNI` hinzu. In der API-Antwort wird `ESDNI` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spanische Sozialversicherungsnummer (SSN)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ESSocialSecurityNumber` hinzu. In der API-Antwort wird `ESSocialSecurityNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Spanische Steueridentifikationsnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `ESTaxIdentificationNumber` hinzu. In der API-Antwort wird `ESTaxIdentificationNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
### <a name="switzerland"></a>Schweiz

:::row:::
    :::column span="":::
        **Entität**

        Schweizerische Sozialversicherungsnummer (AHV)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `CHSocialSecurityNumber` hinzu. In der API-Antwort wird `CHSocialSecurityNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


### <a name="taiwan"></a>Taiwan 

:::row:::
    :::column span="":::
        **Entität**

        Nationale taiwanesische ID-Nummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `TWNationalID` hinzu. In der API-Antwort wird `TWNationalID` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Taiwanesisches Einwohnerzertifikat (ARC/TARC, Resident Certificate)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `TWResidentCertificate` hinzu. In der API-Antwort wird `TWResidentCertificate` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Taiwanesische Reisepassnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `TWPassportNumber` hinzu. In der API-Antwort wird `TWPassportNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

### <a name="united-kingdom"></a>United Kingdom

:::row:::
    :::column span="":::
        **Entität**

        UK- Führerscheinnummer

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `UKDriversLicenseNumber` hinzu. In der API-Antwort wird `UKDriversLicenseNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       UK- Wählerverzeichnisnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `UKNationalInsuranceNumber` hinzu. In der API-Antwort wird `UKNationalInsuranceNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       UK- National Health Service-Nummer (NHS)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `UKNationalHealthNumber` hinzu. In der API-Antwort wird `UKNationalHealthNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       UK- Sozialversicherungsnummer (NINO)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `UKNationalInsuranceNumber` hinzu. In der API-Antwort wird `UKNationalInsuranceNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       UK- oder US- Reisepassnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `USUKPassportNumber` hinzu. In der API-Antwort wird `USUKPassportNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Vereinigtes Königreich: Eindeutige Steuerzahlerreferenznummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `UKUniqueTaxpayerNumber` hinzu. In der API-Antwort wird `UKUniqueTaxpayerNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


### <a name="united-states"></a>USA

:::row:::
    :::column span="":::
        **Entität**

        USA US-Sozialversicherungsnummer (Social Security Number, SSN)

    :::column-end:::
    :::column span="2":::
        **Details**

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `USSocialSecurityNumber` hinzu. In der API-Antwort wird `USSocialSecurityNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::
      **Unterstützte Dokumentsprachen**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       USA Führerscheinnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `USDriversLicenseNumber` hinzu. In der API-Antwort wird `USDriversLicenseNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       US- oder UK- Reisepassnummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `USUKPassportNumber` hinzu. In der API-Antwort wird `USUKPassportNumber` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       USA Steuernummer (Individual Taxpayer Identification Number, ITIN)

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `USIndividualTaxpayerIdentification` hinzu. In der API-Antwort wird `USIndividualTaxpayerIdentification` zurückgegeben, falls es erkannt wird.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       USA DEA-Nummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `DrugEnforcementAgencyNumber` hinzu. In der API-Antwort wird `DrugEnforcementAgencyNumber` zurückgegeben, falls es erkannt wird.
      
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       USA Bankkontonummer

    :::column-end:::
    :::column span="2":::

        Fügen Sie zum Abrufen dieser Entitätskategorie dem `pii-categories`-Parameter `USBankAccountNumber` hinzu. In der API-Antwort wird `USBankAccountNumber` zurückgegeben, falls es erkannt wird.
        
        Wird auch mit `domain=phi`zurückgegeben
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

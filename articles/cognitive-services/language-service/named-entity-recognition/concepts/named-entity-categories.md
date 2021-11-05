---
title: Entitätskategorien, die von der Erkennung benannter Entitäten in Azure Cognitive Service für Language erkannt werden
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über die Entitäten, die das NER-Feature aus unstrukturiertem Text erkennen kann.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: 17c911dadbe0ce741e6e33fd51e0133ff1a67345
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095960"
---
# <a name="supported-named-entity-recognition-ner-entity-categories"></a>Unterstützte Entitätskategorien für die Erkennung benannter Entitäten (NER)

Ermitteln Sie anhand der Informationen in diesem Artikel die Entitätskategorien, die von der [Erkennung benannter Entitäten](../how-to-call.md) (Named Entity Recognition, NER) zurückgegeben werden können. Die NER (Erkennung benannter Entitäten) führt ein Vorhersagemodell aus, um benannte Entitäten aus einem Eingabedokument zu identifizieren und zu kategorisieren.

## <a name="category-person"></a>Kategorie: Person

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Person

    :::column-end:::
    :::column span="2":::
        **Details**

        Namen von Personen
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
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
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

## <a name="category-location"></a>Kategorie: Standort

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Standort

    :::column-end:::
    :::column span="2":::
        **Details**

        natürliche und von Menschen hergestellte Wahrzeichen, Gebäude, geografische Merkmale und geopolitische Entitäten
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Unterkategorien

Die Entität in dieser Kategorie kann die folgenden Unterkategorien aufweisen.

:::row:::
    :::column span="":::
        **Entitätsunterkategorie**

        Geopolitische Entität (GPE)

    :::column-end:::
    :::column span="2":::
        **Details**

        Städte, Länder/Regionen, Staaten
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Strukturell

    :::column-end:::
    :::column span="2":::

        Von Menschen geschaffene Strukturen 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Geografisch

    :::column-end:::
    :::column span="2":::

        Geografische und natürliche Merkmale wie Flüsse, Ozeane und Wüsten
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
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

        Firmen, politische Gruppen, Musikgruppen, Sportvereine, Regierungsstellen und öffentliche Organisationen Nationalitäten und Religionen werden in diesem Entitätstyp nicht berücksichtigt.
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
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
      
    :::column-end:::
    :::column span="2":::
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
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Sportbezogene Organisationen
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

## <a name="category-event"></a>Kategorie: Ereignis

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Ereignis

    :::column-end:::
    :::column span="2":::
        **Details**

        Historische Ereignisse, gesellschaftliche Ereignisse und Naturereignisse
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` und `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Unterkategorien

Die Entität in dieser Kategorie kann die folgenden Unterkategorien aufweisen.

:::row:::
    :::column span="":::
        **Entitätsunterkategorie**

        Kultur

    :::column-end:::
    :::column span="2":::
        **Details**

        Kulturveranstaltungen und Feiertage
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Natural

    :::column-end:::
    :::column span="2":::

        Naturereignisse
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Sportveranstaltungen
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

## <a name="category-product"></a>Kategorie: Produkt

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Produkt

    :::column-end:::
    :::column span="2":::
        **Details**

        Physische Objekte verschiedener Kategorien.
      
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

        Rechenergebnisse
    :::column-end:::
    :::column span="2":::
        **Details**

        Rechenergebnisse
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`   
      
   :::column-end:::
:::row-end:::

## <a name="category-skill"></a>Kategorie: Skill

Diese Kategorie enthält die folgende Entität:

:::row:::
    :::column span="":::
        **Entität**

        Skill

    :::column-end:::
    :::column span="2":::
        **Details**

        Eine Funktion, Kenntnis oder ein Fachwissen
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en` , `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br` 
      
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

        vollständige Postanschrift
      
    :::column-end:::
    :::column span="2":::
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

        Telefonnummern (nur US- und EU-Telefonnummern)
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
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

        E-Mail-Adressen
      
    :::column-end:::
    :::column span="2":::
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

        URLs zu Websites 
      
    :::column-end:::
    :::column span="2":::
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

        Netzwerk-IP-Adressen 
      
    :::column-end:::
    :::column span="2":::
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
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

Entitäten in dieser Kategorie können die folgenden Unterkategorien aufweisen:

#### <a name="subcategories"></a>Unterkategorien

Die Entität in dieser Kategorie kann die folgenden Unterkategorien aufweisen.

:::row:::
    :::column span="":::
        **Entitätsunterkategorie**

        Date

    :::column-end:::
    :::column span="2":::
        **Details**

        Kalenderdatumsangaben
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Time

    :::column-end:::
    :::column span="2":::

        Tageszeiten
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        DateRange

    :::column-end:::
    :::column span="2":::

        Datumsbereiche
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        TimeRange

    :::column-end:::
    :::column span="2":::

        Uhrzeitbereiche
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Duration

    :::column-end:::
    :::column span="2":::

        Zeitspannen
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Set

    :::column-end:::
    :::column span="2":::

        Festgelegte Wiederholungszeiten
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
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

        Number

    :::column-end:::
    :::column span="2":::
        **Details**

        Zahlen.
      
    :::column-end:::
    :::column span="2":::
      **Unterstützte Dokumentsprachen**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Prozentwert

    :::column-end:::
    :::column span="2":::

        Prozentangaben
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Ordinalzahlen

    :::column-end:::
    :::column span="2":::

        Ordinalzahlen
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Age

    :::column-end:::
    :::column span="2":::

        Altersangaben
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Währung

    :::column-end:::
    :::column span="2":::

        Währungen
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Dimensionen

    :::column-end:::
    :::column span="2":::

        Abmessungen und Maße
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Temperatur

    :::column-end:::
    :::column span="2":::

        Temperaturen
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::


## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über NER](../overview.md)

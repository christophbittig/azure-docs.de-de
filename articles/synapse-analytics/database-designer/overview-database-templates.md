---
title: Übersicht über Azure Synapse-Datenbankvorlagen
description: Informationen zu Datenbankvorlagen
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: overview
ms.date: 11/02/2021
ms.custom: template-overview, ignite-fall-2021
ms.openlocfilehash: f34ce2b785aa1515db39cb0266963d8da5c85558
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437426"
---
# <a name="what-is-azure-synapse-database-templates"></a>Was sind Azure Synapse-Datenbankvorlagen?

Daten können viele Formen annehmen, wenn sie mit der Absicht, Geschäftsprobleme zu lösen, von Quellsystemen zu Data Warehouses und Data Marts verlagert werden. Datenbankvorlagen können bei der Transformation von Daten in Erkenntnisse helfen. Eine Datenbankvorlage ist eine Reihe von geschäftsbezogenen und technischen Datendefinitionen, die für die Anforderungen einer bestimmten Branche konzipiert wurden. Diese Vorlagen dienen als Blaupausen, die allgemeine Elemente bereitstellen, die sich aus Best Practices, gesetzlichen Vorgaben und den komplexen Daten- und Analyseanforderungen einer branchenspezifischen Organisation ableiten. 

Diese Informationsblaupausen können von Organisationen verwendet werden, um Datenlösungen für Datengovernance, Berichterstellung, Business Intelligence und erweiterte Analysen zu planen, zu konzipieren und zu entwerfen. Die Datenmodelle bieten integrierte unternehmensweite Informationsarchitekturen, mit denen Sie zügig und vorhersagbar eine bewährte Architektur für Ihre Branchendaten implementieren können. 

Wenn Sie beispielsweise eine Produktempfehlungslösung für Ihre Einzelhandelskunden erstellen, benötigen Sie eine grundlegende Blaupause, um zu verstehen, was die Kunden gekauft haben und welche Transaktion zum Kauf geführt hat. Möglicherweise benötigen Sie auch Informationen zu dem Ladengeschäft, in dem der Kauf getätigt wurde. Außerdem müssen Sie wissen, ob die Kunden an einem Treueprogramm teilnehmen. Für diesen Anwendungsfall sind die folgenden Schemas erforderlich: 

 - Produkt 
 - Transaktion 
 - TransactionLineItem 
 - Kunde 
 - CustomerLoyalty 
 - Speicher 

Sie können diesen Anwendungsfall einrichten, indem Sie die sechs Tabellen in der Datenbankvorlage für den Einzelhandel auswählen. 

[[retail-database-template-example.png|alt=Beispiel einer Vorlage für eine Einzelhandelsdatenbank]]

Eine typische Datenbankvorlage erfüllt die grundlegenden Anforderungen einer bestimmten Branche und umfasst Folgendes: 

 - Unterstützende [Geschäftsbereichsvorlagen](concepts-database-templates.md#business-area-templates).
 - Eine oder mehrere [Unternehmensvorlagen](concepts-database-templates.md#enterprise-templates).  

## <a name="available-database-templates"></a>Verfügbare Datenbankvorlagen 

Derzeit sind in Azure Synapse Studio sechs Datenbankvorlagen verfügbar, mit denen Kunden ihre Data Lake-Datenbank erstellen können. 

 - **Bankwesen**: für Unternehmen, die Bankdaten analysieren
 - **Konsumgüter**: für Hersteller von Waren, die von Verbrauchern gekauft und verwendet werden
 - **Verwalten von Geldmitteln**: für Unternehmen, die Anlagefonds für Investmentunternehmen verwalten
 - **Lebens- und Rentenversicherungen**: für Unternehmen, die Lebens- oder Rentenversicherungen oder beides verkaufen
 - **Sach- und Haftpflichtversicherungen**: für Unternehmen, die Versicherungen für Schäden an Eigentum und verschiedene Formen der Haftungsabdeckung verkaufen
 - **Einzelhandel**: für Verkäufer von Konsumgütern oder Dienstleistungen an Kunden über mehrere Kanäle

Da CO2-Emissionen in allen Branchen ein wichtiges Thema ist, haben wir entsprechende Komponenten in alle verfügbaren Datenbankvorlagen aufgenommen. Diese Komponenten erleichtern Unternehmen ihre Aufgabe, die ihre direkten und indirekten Treibhausgasemissionen nachverfolgen und melden müssen.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über die Möglichkeiten des Database Designers, indem Sie den Link unten verwenden.
- [Datenbank-Vorlagen-Konzept](concepts-database-templates.md)
- [Schnellstart](quick-start-create-lake-database.md)

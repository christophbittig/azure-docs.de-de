---
title: Konzepte für Azure Synapse-Datenbankvorlagen
description: Erfahren Sie mehr über die Datenbankvorlagen in Azure Synapse
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 3ae4d5efc5c80a6a4ff5fdd4c1c5e77ac0d7cb30
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095509"
---
# <a name="lake-database-templates"></a>See-Datenbank-Vorlagen

Azure Synapse Analytics bietet standardisierte Datenbankvorlagen für verschiedene Branchen zur einfachen Verwendung und Erstellung eines Datenbankmodells für die Anforderungen des Unternehmens. Diese Vorlagen enthalten umfangreiche Metadaten zum besseren Verstehen eines Datenmodells. Verwenden Sie diese Vorlagen, um Ihre Lake-Datenbank zu erstellen, und nutzen Sie die analytische Laufzeit von Azure Synapse, um den Geschäftsanwendern Einblicke zu gewähren.

Lernen Sie Konzepte im Zusammenhang mit See-Datenbankvorlagen in Azure Synapse. Verwenden Sie diese Vorlagen, um eine Datenbank mit umfangreichen Metadaten zu erstellen, die das Verstehen und die Produktivität verbessern.  

## <a name="business-area-templates"></a>Vorlagen für Geschäftsbereiche  

Geschäftsbereichsvorlagen bieten die umfassendste und detaillierteste Ansicht der Daten eines Geschäfts- oder Themenbereichs. Geschäftsbereichsmodelle werden auch als Fachbereichs- oder Domänenvorlagen bezeichnet. Geschäftsbereichsvorlagen enthalten Tabellen und Spalten, die für einen bestimmten Geschäftsbereich innerhalb einer Branche relevant sind. Data Stewards, Data Governance-Teams und Geschäftsteams innerhalb eines Unternehmens können die Geschäftsbereichsvorlagen verwenden, um geschäftsorientierte Datenschemata zu erstellen, die detaillierte Kommunikation von Geschäftsanforderungen und -umfang erleichtern. Jede Geschäftsbereichsvorlage wird aus einem gemeinsamen Satz von Entitäten aus der entsprechenden Unternehmensdatenbankvorlage der Branche erstellt, um sicherzustellen, dass die Geschäftsbereichsvorlagen gemeinsame Schlüssel, Attribute und Definitionen haben, die mit anderen Branchenmodellen übereinstimmen. Zum Beispiel sind Buchhaltung und Finanzberichterstattung, Marketing, Budget und Prognosen Geschäftsbereichsvorlagen für viele Branchen wie den Einzelhandel oder das Bankwesen. 

![Geschäftsfeldvorlagen Beispiel](./media/concepts-database-templates/business-area-template-example.png)

## <a name="enterprise-templates"></a>Unternehmensvorlagen 

Unternehmensdatenbankvorlagen enthalten eine Teilmenge von Tabellen, die für ein Unternehmen in einer bestimmten Branche am ehesten von Interesse sind. Sie bietet einen Überblick und beschreibt die Verbindung zwischen den entsprechenden Geschäftsbereichsvorlagen. Diese Vorlagen dienen als Beschleuniger für viele Arten von Großprojekten. Die Bankenvorlage hat zum Beispiel eine Unternehmensvorlage mit dem Namen "Banking". 

![Beispiel für eine Unternehmensvorlage](./media/concepts-database-templates/enterprise-template-example.png)

## <a name="table"></a>Tabelle

Ein Tisch ist ein Objekt mit eigenständiger Existenz, das von anderen Objekten unterschieden werden kann. Zum Beispiel Kunde, Geschäft, Kanal und so weiter.

## <a name="column"></a>Column

Jede Tabelle wird durch eine Reihe von Spalten beschrieben. Jede Spalte hat einen Namen, eine Beschreibung, einen Datentyp und ist mit einer Tabelle verbunden. Die Datenbankvorlagen enthalten etwa 30.000 Spalten. CustomerId ist zum Beispiel eine Spalte in der Tabelle Customer.

## <a name="primary-key"></a>Primary key (Primärschlüssel)

Der Primärschlüssel dient zur eindeutigen Identifizierung der gesamten Tabelle. Sie muss Tupel in einer Tabelle eindeutig identifizieren. So erzwingt beispielsweise ein Schlüssel für die Spalte CustomerId die Eindeutigkeit, um jeden Kunden in der Tabelle Customer zu identifizieren.

## <a name="foreign-key"></a>Fremdschlüssel

Ein Fremdschlüssel ist eine Spalte oder eine Kombination von Spalten, deren Werte einem Primärschlüssel in einer anderen Tabelle entsprechen. Sie hilft, eine Verbindung zwischen zwei Tabellen herzustellen. CustomerId in der Tabelle Transaction steht zum Beispiel für einen Kunden, der eine Transaktion abgeschlossen hat. Ein Fremdschlüssel hat immer eine Beziehung zu einem Primärschlüssel, z. B. zu dem Primärschlüssel CustomerId in der Tabelle Customer.

## <a name="composite-key"></a>Zusammengesetzte Schlüssel

Ein zusammengesetzter Schlüssel setzt sich aus zwei oder mehr Spalten zusammen, die zusammen zur eindeutigen Identifizierung einer Tabelle erforderlich sind. In einer Auftragstabelle können beispielsweise sowohl die Auftragsnummer als auch die ProductId zur eindeutigen Identifizierung eines Datensatzes erforderlich sein.

## <a name="relationships"></a>Beziehungen

Beziehungen sind Assoziationen oder Interaktionen zwischen zwei beliebigen Tabellen. Zum Beispiel sind die Tabellen Kunde und KundeEmail miteinander verbunden. Es gibt zwei Tabellen, die an einer Beziehung beteiligt sind. Es gibt eine übergeordnete Tabelle und eine untergeordnete Tabelle, die oft durch einen Fremdschlüssel verbunden sind. Man könnte sagen, dass die Beziehung von Tabelle zu Tabelle lautet.

## <a name="table-partitions"></a>Tabellenpartitionen

Die Lake-Datenbank ermöglicht es, die zugrunde liegenden Daten für eine Tabelle zu partitionieren, um die Leistung zu verbessern. Sie können die Partitionskonfiguration in den Speichereinstellungen einer Tabelle im Datenbankeditor festlegen.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie fort, die Möglichkeiten des Datenbankdesigners zu erkunden, indem Sie die nachstehenden Links verwenden.
- [Schnellstart](quick-start-create-lake-database.md)
- [See-Datenbank-Konzept](concepts-lake-database.md)

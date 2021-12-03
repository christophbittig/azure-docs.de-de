---
title: Was ist der Azure Cosmos DB-Analysespeicher?
description: Erfahren Sie mehr über den Azure Cosmos DB-Transaktionsspeicher (zeilenbasiert) und -Analysespeicher (spaltenbasiert). Vorteile des Analysespeichers, Auswirkungen auf die Leistung bei umfangreichen Workloads und automatische Synchronisierung von Daten aus dem Transaktionsspeicher in den Analysespeicher
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: 712b1d3e7fde41991f9cea2d62e7e0864224509d
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135202"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Was ist der Azure Cosmos DB-Analysespeicher?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Der Azure Cosmos DB-Analysespeicher ist ein vollständig isolierter Spaltenspeicher, der umfangreiche Analysen für operative Daten in Ihrem Azure Cosmos DB ermöglicht, ohne dass dies Auswirkungen auf Ihre Transaktionsworkloads hat. 

Der Azure Cosmos DB-Transaktionsspeicher ist schemaunabhängig und ermöglicht es Ihnen, Ihre Transaktionsanwendungen zu durchlaufen, ohne sich mit Schema- oder Indexverwaltung befassen zu müssen. Im Gegensatz dazu wird der Azure Cosmos DB-Analysespeicher schematisiert, um die Leistung von analytischen Abfragen zu optimieren. In diesem Artikel finden Sie ausführliche Informationen zum analytischen Speicher.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Herausforderungen bei umfangreichen Analysen operativer Daten

Die operativen Daten aus mehreren Modellen in einem Azure Cosmos DB-Container werden intern in einem indizierten zeilenbasierten „Transaktionsspeicher“ gespeichert. Das Zeilenspeicherformat ist so konzipiert, dass schnelle transaktionale Lese- und Schreibvorgänge mit Antwortzeiten im Millisekundenbereich sowie operative Abfragen möglich sind. Wenn das Dataset sehr umfangreich wird, können komplexe analytische Abfragen hinsichtlich des bereitgestellten Durchsatzes der in diesem Format gespeicherten Daten teuer sein. Ein hoher Verbrauch von bereitgestelltem Durchsatz wirkt sich wiederum auf die Leistung von transaktionalen Workloads aus, die von ihren Echtzeitanwendungen und -Diensten verwendet werden.

Zum Analysieren großer Datenmengen werden operative Daten in der Regel aus dem Transaktionsspeicher von Azure Cosmos DB extrahiert und in einer separaten Datenschicht gespeichert. Die Daten werden beispielsweise in einem Data Warehouse oder Data Lake in einem geeigneten Format gespeichert. Später werden diese Daten für umfangreiche Analysen verwendet und mithilfe einer Compute-Engine (z. B. den Apache Spark-Clustern) analysiert. Diese Trennung der Analysespeicher- und Analysecompute-Ebenen von den operativen Daten führt zu einer zusätzlichen Latenz, da die ETL-Pipelines (Extrahieren, Transformieren, Laden) seltener ausgeführt werden, um die potenziellen Auswirkungen auf Ihre Transaktionsworkloads zu minimieren.

Die ETL-Pipelines weisen zudem bei der Verarbeitung von Aktualisierungen der operativen Daten im Vergleich zur reinen Verarbeitung neu erfasster operativer Daten eine größere Komplexität auf. 

## <a name="column-oriented-analytical-store"></a>Spaltenorientierter Analysespeicher

Der Azure Cosmos DB-Analysespeicher ist auf die Bewältigung der Herausforderungen hinsichtlich Komplexität und Latenz ausgelegt, die sich bei herkömmlichen ETL-Pipelines ergeben. Der Azure Cosmos DB-Analysespeicher kann Ihre operativen Daten automatisch in einen separaten Spaltenspeicher synchronisieren. Das Spaltenspeicherformat eignet sich für umfangreiche analytische Abfragen, die auf optimierte Weise ausgeführt werden können. Dies führt zu einer Verbesserung bei der Latenz solcher Abfragen.

Mithilfe von Azure Synapse Link können Sie jetzt HTAP-Lösungen ohne ETL erstellen, indem Sie eine direkte Verknüpfung von Azure Synapse Analytics mit dem Azure Cosmos DB-Analysespeicher herstellen. Damit können Sie umfangreiche Analysen Ihrer operativen Daten nahezu in Echtzeit ausführen.

## <a name="features-of-analytical-store"></a>Features des Analysespeichers 

Wenn Sie den Analysespeicher für einen Azure Cosmos DB-Container aktivieren, wird basierend auf den operativen Daten in Ihrem Container ein neuer Spaltenspeicher intern erstellt. Dieser Spaltenspeicher wird getrennt vom zeilenorientierten Transaktionsspeicher für diesen Container persistent gespeichert. Die Einfüge-, Aktualisierungs- und Löschvorgänge für Ihre operativen Daten werden automatisch mit dem Analysespeicher synchronisiert. Änderungsfeed oder ETL sind nicht erforderlich, um die Daten zu synchronisieren.

## <a name="column-store-for-analytical-workloads-on-operational-data"></a>Spaltenspeicher für Analyseworkloads operativer Daten

Analyseworkloads umfassen in der Regel Aggregationen und sequenzielle Scans ausgewählter Felder. Durch das Speichern der Daten in einer spaltenweisen Anordnung ermöglicht der Analysespeicher das gemeinsame Serialisieren einer Gruppe von Werten für jedes Feld. Dieses Format reduziert den IOPS-Aufwand zum Scannen oder Berechnen von Statistiken für bestimmte Felder. Dies führt beim Scannen großer Datasets zu einer wesentlichen Verbesserung der Antwortzeiten für Abfragen. 

Wenn die operativen Tabellen beispielsweise das folgende Format haben:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Beispiel für eine operative Tabelle" border="false":::

Im Zeilenspeicher werden die oben angegebenen Daten in einem serialisierten Format nach Zeile auf dem Datenträger gespeichert. Dieses Format ermöglicht schnellere transaktionale Lesevorgänge, Schreibvorgänge und operative Abfragen, z. B. zum Zurückgeben von Informationen über Product1. Wenn das Dataset jedoch immer größer wird und Sie komplexe analytische Abfragen für die Daten ausführen möchten, kann dies teuer werden. Wenn Sie z. B. die Umsatzentwicklung für ein Produkt in der Kategorie „Equipment“ für verschiedene Geschäftseinheiten und Monate abrufen möchten, müssen Sie eine komplexe Abfrage ausführen. Umfangreiche Scanvorgänge für dieses Dataset können in Hinsicht auf den bereitgestellten Durchsatz teuer werden und sich auch auf die Leistung der Transaktionsworkloads auswirken, die Ihre Echtzeitanwendungen und -dienste nutzen.

Der Analysespeicher, bei dem es sich um einen Spaltenspeicher handelt, ist für solche Abfragen besser geeignet, da er ähnliche Datenfelder gemeinsam serialisiert und die Datenträger-IOPS verringert.

In der folgenden Abbildung ist der Transaktions- bzw. Zeilenspeicher im Vergleich zum Analyse- bzw. Spaltenspeicher in Azure Cosmos DB dargestellt:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Transaktions-/Zeilenspeicher im Vergleich zum Analyse-/Spaltenspeicher in Azure Cosmos DB" border="false":::

## <a name="decoupled-performance-for-analytical-workloads"></a>Entkoppelte Leistung für Analyseworkloads

Analytische Abfragen haben keine Auswirkung auf die Leistung der Transaktionsworkloads, da der Analysespeicher vom Transaktionsspeicher getrennt ist.  Dem Analysespeicher müssen keine separaten Anforderungseinheiten (Request Units, RUs) zugewiesen werden.

## <a name="auto-sync"></a>Automatische Synchronisierung

Die automatische Synchronisierung bezieht sich auf die vollständig verwaltete Funktion von Azure Cosmos DB, bei der Einfüge-, Aktualisierungs- und Löschvorgänge für operative Daten automatisch nahezu in Echtzeit vom Transaktionsspeicher in den Analysespeicher synchronisiert werden. Die Wartezeit für die automatische Synchronisierung liegt normalerweise innerhalb von 2 Minuten. In Fällen, in denen eine Datenbank mit gemeinsam genutztem Durchsatz und einer großen Anzahl von Containern verwendet wird, kann die Wartezeit für die automatische Synchronisierung einzelner Containern länger sein und bis zu 5 Minuten betragen. Wir möchten gerne mehr darüber erfahren, inwieweit diese Wartezeit für Ihre Szenarien geeignet ist. Wenden Sie sich dazu an das [Azure Cosmos DB-Team](mailto:cosmosdbsynapselink@microsoft.com).

Am Ende jeder Ausführung des automatischen Synchronisierungsprozesses stehen Ihre Transaktionsdaten sofort für Azure Synapse Analytics-Laufzeiten zur Verfügung:

* Spark-Pools in Azure Synapse Analytics können alle Daten, einschließlich der neuesten Updates, über Spark-Tabellen lesen, die automatisch aktualisiert werden, oder über den Befehl `spark.read`, der immer den letzten Zustand der Daten liest.

*  SQL Serverless-Pools in Azure Synapse Analytics können alle Daten, einschließlich der neuesten Updates, über Ansichten lesen, die automatisch aktualisiert werden, oder über `SELECT` zusammen mit den ` OPENROWSET`-Befehlen, die immer den letzten Status der Daten lesen.

> [!NOTE]
> Ihre Transaktionsdaten werden auch dann mit dem Analysespeicher synchronisiert, wenn Ihre Transaktionsgültigkeitsdauer kürzer als zwei Minuten ist. 

## <a name="scalability--elasticity"></a>Skalierbarkeit und Elastizität

Mithilfe der horizontalen Partitionierung kann der Transaktionsspeicher von Azure Cosmos DB den Speicher und den Durchsatz ohne Ausfallzeiten elastisch skalieren. Die horizontale Partitionierung im Transaktionsspeicher bietet Skalierbarkeit und Elastizität bei der automatischen Synchronisierung, um sicherzustellen, dass die Daten nahezu in Echtzeit in den Analysespeicher synchronisiert werden. Die Datensynchronisierung erfolgt unabhängig vom Durchsatz des transaktionalen Datenverkehrs, ganz gleich, ob es sich um 1000 Vorgänge/Sek. oder 1 Million Vorgänge/Sek. handelt. Zudem wirkt sie sich nicht auf den bereitgestellten Durchsatz im Transaktionsspeicher aus. 

## <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Automatische Handhabung von Schemaaktualisierungen

Der Azure Cosmos DB-Transaktionsspeicher ist schemaunabhängig und ermöglicht es Ihnen, Ihre Transaktionsanwendungen zu durchlaufen, ohne sich mit Schema- oder Indexverwaltung befassen zu müssen. Im Gegensatz dazu wird der Azure Cosmos DB-Analysespeicher schematisiert, um die Leistung von analytischen Abfragen zu optimieren. Mit der Funktion der automatischen Synchronisierung verwaltet Azure Cosmos DB den Schemarückschluss über die neuesten Aktualisierungen aus dem Transaktionsspeicher. Außerdem wird die Schemadarstellung im Analysespeicher standardmäßig verwaltet, einschließlich der Handhabung geschachtelter Datentypen.

Während Ihr Schema weiterentwickelt wird und im Laufe der Zeit neue Eigenschaften hinzugefügt werden, stellt der Analysespeicher ein zusammengefasstes Schema für alle verlaufsbezogenen Schemata im Transaktionsspeicher automatisch dar.

> [!NOTE]
> Im Kontext des Analysespeichers betrachten wir die folgenden Strukturen als Eigenschaft:
> * JSON-„Elemente“ oder „Zeichenfolgen-Wert-Paare, die durch ein `:`getrennt sind.“
> * JSON-Objekte, getrennt durch `{` und `}`.
> * JSON-Arrays, getrennt durch `[` und `]`.


### <a name="schema-constraints"></a>Schemaeinschränkungen

Die folgenden Einschränkungen gelten für die operativen Daten in Azure Cosmos DB, wenn Sie den Analysespeicher aktivieren, damit das Schema automatisch abgeleitet und richtig dargestellt wird:

* Sie können maximal 1000 Eigenschaften auf jeder Schachtelungsebene im Schema und eine maximale Schachtelungstiefe von 127 festlegen.
  * Nur die ersten 1000 Eigenschaften werden im Analysespeicher dargestellt.
  * Nur die ersten 127 Eigenschaften werden im Analysespeicher dargestellt.
  * Die erste Ebene eines JSON-Dokuments ist die Stammebene `/`.
  * Eigenschaften in der ersten Ebene des Dokuments werden als Spalten dargestellt.


* Beispielszenarien:
  * Wenn die erste Ebene Ihres Dokuments 2000 Eigenschaften aufweist, werden nur die ersten 1000 dargestellt.
  * Wenn Ihre Dokumente 5 Ebenen mit jeweils 200 Eigenschaften aufweisen, werden alle Eigenschaften dargestellt.
  * Wenn Ihre Dokumente 10 Ebenen mit jeweils 400 Eigenschaften aufweisen, werden nur die beiden ersten Ebenen vollständig im Analysespeicher dargestellt. Die Hälfte der dritten Ebene wird ebenfalls dargestellt.

* Das folgende hypothetische Dokument enthält vier Eigenschaften und drei Ebenen.
  * Die Ebenen sind `root`, `myArray` und die geschachtelte Struktur innerhalb von `myArray`.
  * Die Eigenschaften sind `id`, `myArray`, `myArray.nested1` und `myArray.nested2`.
  * Die Darstellung des Analysespeichers enthält zwei Spalten, `id` und `myArray`. Sie können Spark- oder T-SQL-Funktionen verwenden, um die geschachtelten Strukturen auch als Spalten verfügbar zu machen.


```json
{
  "id": "1",
  "myArray": [
    "string1",
    "string2",
    {
      "nested1": "abc",
      "nested2": "cde"
    }
  ]
}
```

* Während JSON-Dokumente (und Cosmos DB-Sammlungen/Container) im Hinblick auf die Eindeutigkeit zwischen Groß- und Kleinschreibung unterscheiden, ist dies beim Analysespeicher nicht der Fall.

  * **Im gleichen Dokument:** Namen von Eigenschaften in der gleichen Ebene sollten im Vergleich zur Groß-/Kleinschreibung eindeutig sein. Das folgende JSON-Dokument enthält z. B. „Name“ und „name“ auf der gleichen Ebene. Obwohl es sich um ein gültiges JSON-Dokument handelt, erfüllt es nicht die Bedingung der Eindeutigkeit und wird daher nicht vollständig im Analysespeicher dargestellt. In diesem Beispiel sind „Name“ und „name“ identisch, wenn sie ohne Berücksichtigung der Groß-/Kleinschreibung verglichen werden. Wird nur `"Name": "fred"` im Analysespeicher dargestellt, da es das erste Vorkommen ist. Und `"name": "john"` wird überhaupt nicht dargestellt.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **In unterschiedlichen Dokumenten:** Eigenschaften in der gleichen Ebene und mit dem gleichen Namen, aber in unterschiedlichen Fällen, werden innerhalb der gleichen Spalte dargestellt, wobei das Namensformat des ersten Vorkommens verwendet wird. Beispielsweise haben die folgenden JSON-Dokumente `"Name"` und `"name"` auf derselben Ebene. Da das erste Dokumentformat `"Name"` ist, wird dies verwendet, um den Eigenschaftsnamen im Analysespeicher darzustellen. Anders ausgedrückt: der Spaltenname im Analysespeicher lautet `"Name"` . Sowohl `"fred"` als auch werden `"john"` in der `"Name"` Spalte dargestellt.


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* Das erste Dokument der Auflistung definiert das anfängliche Schema des Analysespeichers.
  * Dokumente, die mehr Eigenschaften als das anfängliche Schema aufweisen, generieren neue Spalten im Analysespeicher.
  * Spalten können nicht entfernt werden.
  * Das Löschen aller Dokumente in einer Sammlung setzt das Schema des analytischen Speichers nicht zurück.
  * Eine Versionierung des Schemas gibt es nicht. Die letzte Version, die aus dem Transaktionsspeicher abgeleitet wird, ist die, die Sie im Analysespeicher sehen werden.

* Zurzeit kann Azure Synapse Spark keine Eigenschaften lesen, deren Namen einige der unten aufgeführten Sonderzeichen enthalten. Azure Synapse SQL (serverlos) ist nicht betroffen.
  * : (Doppelpunkt)
  * ` (Graviszeichen, Accent grave)
  * , (Komma)
  * ; (Semikolon)
  * {}
  * ()
  * \n
  * \t
  * = (Gleichheitszeichen)
  * " (Anführungszeichen)
 
* Wenn Sie Eigenschaftennamen haben, die die oben aufgeführten Zeichen verwenden, sind die folgenden Alternativen verfügbar:
   * Ändern Sie Ihr Datenmodell im Voraus, um diese Zeichen zu vermeiden.
   * Da die Schemazurücksetzung derzeit nicht unterstützt wird, können Sie Ihre Anwendung ändern, um eine redundante Eigenschaft mit einem ähnlichen Namen hinzuzufügen, um diese Zeichen zu vermeiden.
   * Verwenden Sie den Änderungsfeed, um eine materialisierte Sicht Ihres Containers ohne diese Zeichen in Eigenschaftennamen zu erstellen.
   * Verwenden Sie die neue Spark-Option `dropColumn`, um die betroffenen Spalten zu ignorieren, wenn Daten in einen DataFrame geladen werden. Die Syntax zum Löschen einer hypothetischen Spalte namens „FirstName,LastNAme“, die ein Komma enthält, ist:

```Python
df = spark.read\
     .format("cosmos.olap")\
     .option("spark.synapse.linkedService","<your-linked-service-name>")\
     .option("spark.synapse.container","<your-container-name>")\
     .option("spark.synapse.dropColumn","FirstName,LastName")\
     .load()
```

* Azure Synapse Spark unterstützt jetzt Eigenschaften, deren Namen Leerzeichen enthalten.

* Die folgenden BSON-Datentypen werden nicht unterstützt und nicht im Analysespeicher dargestellt:
  * Decimal128
  * Regular Expression
  * DB-Zeiger
  * JavaScript
  * Symbol
  * MinKey/MaxKey 

* Wenn Sie DateTime-Zeichenfolgen verwenden, die dem ISO 8601 UTC-Standard entsprechen, erwarten Sie das folgende Verhalten:
  * Spark-Pools in Azure Synapse stellen diese Spalten als `string` dar.
  * SQL Serverless-Pools in Azure Synapse stellen diese Spalten als `varchar(8000)` dar.

* SQL serverlose Pools in Azure Synapse unterstützen Ergebnismengen mit bis zu 1.000 Spalten, und das Verfügbarmachen geschachtelter Spalten zählt auch für diesen Grenzwert. Berücksichtigen Sie diese Informationen, wenn Sie Ihre Datenarchitektur entwerfen und Ihre Transaktionsdaten modellieren.


### <a name="schema-representation"></a>Schemadarstellung

Im Analysespeicher gibt es zwei Arten der Schemadarstellung. Diese definieren die Methode der Schemadarstellung für alle Container im Datenbankkonto und weisen Kompromisse zwischen der Einfachheit der Abfrage und der Einfachheit einer umfassenderen spaltenweisen Darstellung für polymorphe Schemas auf.

* Die klar definierte Schemadarstellung für die Standardoption für SQL (CORE)-API-Konten. 
* Die Darstellung des Schemas mit vollständiger Genauigkeit ist die Standardoptionen für die API von Azure Cosmos DB für MongoDB-Konten.

#### <a name="full-fidelity-schema-for-sql-api-accounts"></a>Schema mit vollständiger Genauigkeit für SQL-API-Konten

Es ist möglich, anstelle der Standardoption das Schema mit vollständiger Genauigkeit für SQL (Core)-API-Konten zu verwenden. Dazu legen Sie den Schematyp beim erstmaligen Aktivieren von Synapse Link für ein Cosmos DB-Konto fest. Nachfolgend sind die Überlegungen zum Ändern des Standardtyps für die Schemadarstellung aufgeführt:

 * Diese Option ist nur für Konten gültig, für die Synapse Link **nicht** bereits aktiviert ist.
 * Es ist nicht möglich, die Schemadarstellung von klar definiert auf vollständige Genauigkeit zurückzusetzen oder umgekehrt.
 * Derzeit sind Azure Cosmos DB-API für MongoDB-Konten mit dieser Möglichkeit zur Änderung der Schemadarstellung nicht kompatibel. Alle MongoDB-Konten weisen immer eine Schemadarstellung mit vollständiger Genauigkeit auf.
 * Derzeit kann diese Änderung nicht über das Azure-Portal vorgenommen werden. Alle Datenbankkonten, für die Synapse Link über das Azure-Portal aktiviert wurde, weisen den Standardtyp für die Schemadarstellung auf, das klar definierte Schema.
 
Die Entscheidung über die Art der Schemadarstellung muss zeitgleich mit der Aktivierung von Synapse Link für das Konto mithilfe der Azure CLI oder mit PowerShell getroffen werden.
 
 Mit der Azure CLI:
 ```cli
 az cosmosdb create --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --subscription MySubscription --analytical-storage-schema-type "FullFidelity" --enable-analytical-storage true
 ```
 
> [!NOTE]
> Ersetzen Sie im obigen Befehl `create` mit `update` für bestehende Konten.
 
  Mit der PowerShell:
  ```
   New-AzCosmosDBAccount -ResourceGroupName MyResourceGroup -Name MyCosmosDBDatabaseAccount  -EnableAnalyticalStorage true -AnalyticalStorageSchemaType "FullFidelity"
   ```
 
> [!NOTE]
> Ersetzen Sie im obigen Befehl `New-AzCosmosDBAccount` mit `Update-AzCosmosDBAccount` für bestehende Konten.
 


#### <a name="well-defined-schema-representation"></a>Genau definierte Schemadarstellung

Die genau definierte Schemadarstellung erstellt eine einfache tabellarische Darstellung der schemaunabhängigen Daten im Transaktionsspeicher. Bei der genau definierten Schemadarstellung gibt es die folgenden Überlegungen:

* Das erste Dokument definiert das Basisschema. Die Eigenschaft muss in allen Dokumenten immer denselben Typ aufweisen. Es gelten nur die folgende Ausnahmen:
  * Von null in einen anderen Datentyp. Das erste Vorkommen ungleich null definiert den Spaltendatentyp. Alle Dokumente, die nicht dem ersten Datentyp ungleich null folgen, werden im Analysespeicher nicht dargestellt.
  * Von `float` in `integer`. Alle Dokumente werden im Analysespeicher dargestellt.
  * Von `integer` in `float`. Alle Dokumente werden im Analysespeicher dargestellt. Um diese Daten jedoch mit Azure Synapse SQL serverlosen Pools zu lesen, müssen Sie eine WITH-Klausel verwenden, um die Spalte in `varchar` zu konvertieren. Und nach dieser anfänglichen Konvertierung ist es möglich, sie erneut in eine Zahl zu konvertieren. Schauen Sie sich das folgende Beispiel an, bei dem **num** der erste Wert eine Ganzzahl und der zweite ein Gleitkommazahl war.

```SQL
SELECT CAST (num as float) as num
FROM OPENROWSET(PROVIDER = 'CosmosDB',
                CONNECTION = '<your-connection',
                OBJECT = 'IntToFloat',
                SERVER_CREDENTIAL = 'your-credential'
) 
WITH (num varchar(100)) AS [IntToFloat]
```

  * Eigenschaften, die nicht dem Basisschemadatentyp folgen, werden im Analysespeicher nicht dargestellt. Betrachten Sie beispielsweise die beiden folgenden Dokumente, und dass das erste Dokument das Basisschema des Analysespeichers definiert hat. Das zweite Dokument, wo `id` `2` ist, hat kein genau definiertes Schema, da die Eigenschaft `"a"` eine Zeichenkette ist und das erste Dokument `"a"` als Zahl hat. In diesem Fall registriert der Analysespeicher den Datentyp von `"a"` als `integer` für die Lebensdauer des Containers. Das zweite Dokument wird weiterhin im Analysespeicher enthalten sein, aber seine Eigenschaft `"a"` nicht.
  
    * `{"id": "1", "a":123}` 
    * `{"id": "2", "a": "str"}`
     
 > [!NOTE]
 > Die obige Bedingung gilt nicht für die Null-Eigenschaften. Beispielsweise ist `{"a":123} and {"a":null}` weiterhin genau definiert.

* Arraytypen müssen einen einzelnen wiederholten Typ enthalten. `{"a": ["str",12]}` ist beispielsweise kein genau definiertes Schema, weil das Array eine Mischung aus ganzzahligen Typen und Zeichenfolgetypen enthält.

> [!NOTE]
> Wenn der Azure Cosmos DB-Analysespeicher der genau definierten Schemadarstellung folgt und die vorstehende Spezifikation durch bestimmte Elemente verletzt wird, werden diese Elemente in den Analysespeicher nicht einbezogen.

* Erwarten Sie unterschiedliches Verhalten in Bezug auf verschiedene Typen in einem gut definierten Schema:
  * Spark-Pools in Azure Synapse stellen diese Werte als `undefined` dar.
  * SQL Serverless-Pools in Azure Synapse stellen diese Werte als `NULL` dar.

* Erwarten Sie ein anderes Verhalten hinsichtlich expliziter `null` Werte:
  * Spark-Pools in Azure Synapse lesen diese Werte als `0` (null). Außerdem ändert er sich in `undefined`, sobald die Spalte einen Wert ungleich NULL aufweist.
  * SQL Serverless-Pools in Azure Synapse lesen diese Werte als `NULL`.
    
* Erwarten Sie ein anderes Verhalten hinsichtlich expliziter  Werte:
  * Spark-Pools in Azure Synapse stellen diese Spalten als `undefined` dar.
  * SQL Serverless-Pools in Azure Synapse stellen diese Spalten als `NULL` dar.


#### <a name="full-fidelity-schema-representation"></a>Schemadarstellung mit vollständiger Genauigkeit

Die Schemadarstellung mit vollständiger Genauigkeit ist so konzipiert, dass sie die gesamte Breite von polymorphen Schemata in den schemaunabhängigen operativen Daten verarbeitet. In dieser Schemadarstellung werden keine Elemente aus dem Analysespeicher gelöscht, selbst wenn die genau definierten Schemaeinschränkungen (d. h. keine Felder mit gemischtem Datentyp oder Arrays mit gemischtem Datentyp) verletzt werden.

Dies wird erreicht, indem die Blatteigenschaften der operativen Daten in den Analysespeicher mit unterschiedlichen Spalten – basierend auf dem Datentyp der Werte in der-Eigenschaft – übersetzt werden. Die Namen der Blatteigenschaften werden mit Datentypen als Suffix im Analysespeicherschema erweitert, sodass sie Abfragen ohne Mehrdeutigkeit sein können.

In der Schemadarstellung mit vollständiger Genauigkeit generiert jeder Datentyp jeder Eigenschaft eine Spalte für den jeweiligen Datentyp. Jede davon zählt als eine der maximal 1.000 Eigenschaften.

Sehen Sie sich beispielsweise das folgende Beispieldokument im Transaktionsspeicher an:

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

Die Blatteigenschaft `streetNo` innerhalb des geschachtelten Objekts `address` wird im Analysespeicherschema als die Spalte `address.object.streetNo.int32` dargestellt. Der Datentyp wird der Spalte als Suffix hinzugefügt. Wenn dem Transaktionsspeicher ein weiteres Dokument hinzugefügt wird, in dem der Wert der Blatteigenschaft `streetNo` „123“ lautet (dies ist eine Zeichenfolge), wird das Schema des Analysespeichers automatisch weiterentwickelt, ohne dass dadurch der Typ einer zuvor geschriebenen Spalte geändert wird. Dem Analysespeicher wurde eine neue Spalte als `address.object.streetNo.string` hinzugefügt. Darin wird dieser Wert „123“ gespeichert.

**Zuordnung des Datentyps „An-Suffix“**

Hier ist eine Zuordnung aller Eigenschaftsdatentypen und deren Suffixdarstellungen im Analysespeicher:

|Ursprünglicher Datentyp  |Suffix  |Beispiel  |
|---------|---------|---------|
| Double |  ".float64" |    24,99|
| Array | ".array" |    ["a", "b"]|
|Binary | ".binary" |0|
|Boolean    | ".bool"   |True|
|Int32  | ".int32"  |123|
|Int64  | ".int64"  |255486129307|
|Null   | ".null"   | NULL|
|String|    ".string" | "ABC"|
|Timestamp |    ".timestamp" |  Timestamp(0, 0)|
|Datetime   |".date"    | ISODate("2020-08-21T07:43:07.375Z")|
|ObjectID   |".objectId"    | ObjectId("5f3f7b59330ec25c132623a2")|
|Dokument   |".object" |    {"a": "a"}|

* Erwarten Sie ein anderes Verhalten hinsichtlich expliziter `null` Werte:
  * Spark-Pools in Azure Synapse lesen diese Werte als `0` (null).
  * SQL Serverless-Pools in Azure Synapse lesen diese Werte als `NULL`.
  
* Erwarten Sie ein anderes Verhalten hinsichtlich expliziter  Werte:
  * Spark-Pools in Azure Synapse stellen diese Spalten als `undefined` dar.
  * SQL Serverless-Pools in Azure Synapse stellen diese Spalten als `NULL` dar.

## <a name="cost-effective-archival-of-historical-data"></a>Kostengünstige Archivierung von Verlaufsdaten

Datentiering bezieht sich auf die Trennung von Daten zwischen Speicherinfrastrukturen, die für verschiedene Szenarien optimiert sind. Dadurch wird die Gesamtleistung und die Kosteneffizienz des End-to-End-Datenstapels verbessert. Mit dem Analysespeicher unterstützt Azure Cosmos DB jetzt das automatische Tiering von Daten aus dem Transaktionsspeicher in den Analysespeicher mit unterschiedlichen Datenlayouts. Mit dem Analysespeicher, der im Vergleich zum Transaktionsspeicher in Bezug auf die Speicherkosten optimiert ist, können Sie deutlich längere Horizonte operativer Daten für Verlaufsanalysen aufbewahren.

Nachdem der Analysespeicher aktiviert wurde, können Sie basierend auf den Anforderungen an die Datenaufbewahrung der Transaktionsworkloads die Gültigkeitsdauer des Transaktionsspeichers (Transaktionale Gültigkeitsdauer) so konfigurieren, dass Datensätze nach einem bestimmten Zeitraum automatisch aus dem Transaktionsspeicher gelöscht werden. Entsprechend können Sie mit der Gültigkeitsdauer des Analysespeichers (Analytische Gültigkeitsdauer) den Lebenszyklus von Daten verwalten, die im Analysespeicher unabhängig vom Transaktionsspeicher aufbewahrt werden. Das Aktivieren des Analysespeichers und Konfigurieren der Gültigkeitsdauereigenschaften ermöglicht Ihnen ein nahtloses Tiering und Definieren des Datenaufbewahrungszeitraums für die beiden Speicher.

> [!NOTE]
>Derzeit unterstützt der Analysespeicher keine Sicherung und Wiederherstellung. Ihre Sicherungsrichtlinie kann nicht auf Grundlage des Analysespeichers geplant werden. Weitere Informationen finden Sie im Abschnitt zu Einschränkungen in [diesem](synapse-link.md#limitations) Dokument. Beachten Sie, dass die Daten im Analysespeicher ein anderes Schema als im Transaktionsspeicher aufweisen. Sie können zwar Momentaufnahmen Ihrer Analysespeicherdaten generieren, ohne dass Kosten für RUs entstehen, doch können wir nicht garantieren, dass anhand dieser Momentaufnahme eine Rückführung in den Transaktionsspeicher erfolgen kann. Dieser Prozess wird nicht unterstützt.

## <a name="global-distribution"></a>Globale Verteilung

Wenn Sie über ein global verteiltes Azure Cosmos DB-Konto verfügen, ist es nach dem Aktivieren des Analysespeichers für einen Container in allen Regionen für dieses Konto verfügbar.  Änderungen an operativen Daten werden in allen Regionen global repliziert. Sie können analytische Abfragen effektiv für die nächstgelegene regionale Kopie Ihrer Daten in Azure Cosmos DB ausführen.

## <a name="partitioning"></a>Partitionierung

Die Partitionierung des Analysespeichers ist vollkommen unabhängig von der Partitionierung im Transaktionsspeicher. Daten im Analysespeicher sind standardmäßig nicht partitioniert. Wenn Ihre analytischen Abfragen häufig Filter verwenden, können Sie basierend auf diesen Feldern partitionieren, um die Abfrageleistung zu verbessern. Weitere Informationen finden Sie in den Artikeln zur [Einführung in die benutzerdefinierte Partitionierung](custom-partitioning-analytical-store.md) und [Konfigurieren der benutzerdefinierten Partitionierung](configure-custom-partitioning.md).  

## <a name="security"></a>Sicherheit

* Die **Authentifizierung mit dem Analysespeicher** erfolgt genauso, wie mit dem Transaktionsspeicher für eine bestimmte Datenbank. Sie können Primärschlüssel oder Schlüssel mit Leseberechtigung für die Authentifizierung verwenden. Sie können den verknüpften Dienst in Synapse Studio nutzen, um zu verhindern, dass die Azure Cosmos DB-Schlüssel in den Spark-Notebooks eingefügt werden. Für die serverlose Azure Synapse SQL können Sie die SQL Anmeldeinformationen verwenden, um auch das Einfügen der Azure Cosmos-Datenbankschlüssel in die SQL Notebooks zu verhindern. Der Zugriff auf diese verknüpften Dienste oder diese Anmeldeinformationen steht allen Personen zur Verfügung, die Zugriff auf den Arbeitsbereich haben.

* **Netzwerkisolation mithilfe privater Endpunkte:** Sie können den Netzwerkzugriff auf die Daten in den Transaktions- und Analysespeichern unabhängig voneinander steuern. Die Netzwerkisolation erfolgt über separate verwaltete private Endpunkte für jeden Speicher in verwalteten virtuellen Netzwerken in Azure Synapse-Arbeitsbereichen. Weitere Informationen finden Sie im Artikel [Konfigurieren privater Endpunkte für den Analysespeicher](analytical-store-private-endpoints.md).

* **Datenverschlüsselung mit kundenseitig verwalteten Schlüsseln:** Sie können Daten nahtlos im Transaktions- und Analysespeicher verschlüsseln und dabei die gleichen kundenseitig verwalteten Schlüssel automatisiert und transparent verwenden. Azure Synapse Link unterstützt nur das Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe der verwalteten Identität Ihres Azure Cosmos DB-Kontos. Sie müssen die verwaltete Identität Ihres Kontos in Ihrer Azure Key Vault-Zugriffsrichtlinie konfigurieren, bevor Sie den [Azure Synapse Link für Ihr Konto aktivieren](configure-synapse-link.md#enable-synapse-link). Weitere Informationen finden Sie in dem Artikel [Konfigurieren von kundenseitig verwalteten Schlüsseln mithilfe verwalteter Identitäten eines Azure Cosmos DB-Kontos](how-to-setup-cmk.md#using-managed-identity).

## <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Unterstützung für mehrere Azure Synapse Analytics-Laufzeiten

Der Analysespeicher ist optimiert, um Skalierbarkeit, Elastizität und Leistung für Analyseworkloads ohne jegliche Abhängigkeit von den Computelaufzeiten bereitzustellen. Die Speichertechnologie ist selbst verwaltet, um Ihre Analyseworkloads ohne manuellen Aufwand zu optimieren.

Durch das Entkoppeln des Analysespeichersystems vom Analysecomputesystem können Daten im Analysespeicher von Azure Cosmos DB gleichzeitig aus den verschiedenen von Azure Synapse Analytics unterstützten Analyselaufzeiten abgefragt werden. Ab sofort werden Apache Spark und serverlose SQL-Pools mit Azure Cosmos DB-Analysespeicher von Azure Synapse Analytics unterstützt.

> [!NOTE]
> Sie können nur mithilfe der Azure Synapse Analytics-Laufzeiten aus dem Analysespeicher lesen. Umgekehrt können auch Azure Synapse Analytics-Laufzeiten nur aus dem Analysespeicher lesen. Nur der automatische Synchronisierungsprozess kann Daten im Analysespeicher ändern. Mithilfe des Spark-Pools in Azure Synapse Analytics können Sie Daten unter Verwendung des integrierten Azure Cosmos DB-OLTP-SDK in den Transaktionsspeicher zurückschreiben.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Preise

Der Analysespeicher folgt einem nutzungsbasierten Preismodell, bei dem Folgendes in Rechnung gestellt wird:

* Speicher: Die Menge an Daten, die im Analysespeicher pro Monat aufbewahrt werden, einschließlich Verlaufsdaten gemäß der analytischen Gültigkeitsdauer.

* Analyseschreibvorgänge: Die vollständig verwaltete Synchronisierung von Aktualisierungen operativer Daten aus dem Transaktionsspeicher in den Analysespeicher (automatische Synchronisierung).

* Analyselesevorgänge: Lesevorgänge, die für den Analysespeicher in Azure Synapse Analytics-Laufzeiten für Spark- und serverlose SQL-Pools ausgeführt werden.

Die Preise für den Analysespeicher sind vom Preismodell für den Transaktionsspeicher getrennt. Es gibt kein Konzept für bereitgestellte RUs im Analysespeicher. Ausführliche Informationen zum Preismodell für den Analysespeicher finden Sie auf der [Azure Cosmos DB – Preisseite](https://azure.microsoft.com/pricing/details/cosmos-db/).

Auf Daten im Analysespeicher kann nur über Azure Synapse Link zugegriffen werden. Dies erfolgt in den Azure Synapse Analytics-Runtimes: Azure Synapse Apache Spark-Pools und Azure Synapse serverlose SQL-Pools. Ausführliche Informationen zum Preismodell für den Zugriff auf Daten im Analysespeicher finden Sie auf der [Azure Synapse Analytics-Preisseite](https://azure.microsoft.com/pricing/details/synapse-analytics/).

Wenn Sie eine allgemeine Kostenschätzung für das Aktivieren des Analysespeichers in einem Azure Cosmos DB-Container aus der Perspektive des Analysespeichers erhalten möchten, können Sie den [Azure Cosmos DB Capacity Planner](https://cosmos.azure.com/capacitycalculator/) verwenden und so eine Schätzung der Kosten für den Analysespeicher und Analyseschreibvorgänge abrufen. Die Kosten für Analyselesevorgänge hängen von den Merkmalen der Analyseworkloads ab, doch als grobe Schätzung führt das Scannen von 1 TB Daten im Analysespeicher in der Regel zu 130.000 Analyselesevorgängen und somit zu Kosten von 0,065 US-Dollar.

> [!NOTE]
> Schätzungen zu Lesevorgängen in Analysespeichern sind im Cosmos DB Kostenrechner nicht enthalten, da sie eine Funktion Ihrer analytischen Workload sind. Während die obige Schätzung für das Scannen von 1 TB Daten im Analysespeicher gilt, reduziert das Anwenden von Filtern die Menge gescannter Daten, und dies bestimmt die genaue Anzahl analytischer Lesevorgänge gemäß des nutzungsbasierten Preismodells. Ein Proof of Concept für die analytische Workload bietet einen exakteren Schätzwert der Anzahl analytischer Lesevorgänge. Diese Schätzung enthält nicht die Kosten für Azure Synapse Analytics.


## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Analytische Gültigkeitsdauer (TTL)

Die analytische Gültigkeitsdauer gibt an, wie lange Daten in Ihrem Analysespeicher für einen Container aufbewahrt werden sollen. 

Wenn der Analysespeicher aktiviert ist, werden Einfüge-, Aktualisierungs- und Löschvorgänge für operative Daten unabhängig von der Konfiguration der transaktionalen Gültigkeitsdauer automatisch aus dem Transaktionsspeicher in den Analysespeicher synchronisiert. Die Aufbewahrung dieser operativen Daten im Analysespeicher kann durch den Wert der analytischen Gültigkeitsdauer auf Containerebene gesteuert werden, wie nachfolgend beschrieben:

Die analytische Gültigkeitsdauer für einen Container wird mithilfe der Eigenschaft `AnalyticalStoreTimeToLiveInSeconds` festgelegt:

* Wenn der Wert auf „0“ festgelegt ist, fehlt (oder auf NULL festgelegt ist), wird der Analysespeicher deaktiviert, und es werden keine Daten aus dem Transaktionsspeicher in den Analysespeicher repliziert.

* Ist die Angabe vorhanden und der Wert auf „-1“ festgelegt, werden im Analysespeicher alle Verlaufsdaten aufbewahrt, unabhängig von der Aufbewahrung der Daten im Transaktionsspeicher. Diese Einstellung gibt an, dass Ihre operativen Daten im Analysespeicher unbegrenzt aufbewahrt werden.

* Ist die Angabe vorhanden und der Wert auf eine beliebige positive Zahl „n“ festgelegt, läuft die Gültigkeit von Elementen im Analysespeicher „n“ Sekunden nach dem Zeitpunkt der letzten Änderung im Transaktionsspeicher ab. Diese Einstellung kann genutzt werden, wenn Sie die operativen Daten für einen begrenzten Zeitraum im Analysespeicher aufbewahren möchten, unabhängig von der Aufbewahrung der Daten im Transaktionsspeicher.

Zu berücksichtigende Punkte:

*   Nachdem der Analysespeicher mit einem Wert für die analytische Gültigkeitsdauer aktiviert wurde, kann er später auf einen anderen gültigen Wert aktualisiert werden. 
*   Während die transaktionale Gültigkeitsdauer auf Container- oder Elementebene festgelegt werden kann, kann die analytische Gültigkeitsdauer derzeit nur auf Containerebene festgelegt werden.
*   Sie können eine längere Aufbewahrung Ihrer operativen Daten im Analysespeicher erzielen, indem Sie die analytische Gültigkeitsdauer mindestens auf den Wert der transaktionalen Gültigkeitsdauer auf Containerebene festlegen.
*   Der Analysespeicher kann auf die Spiegelung des Transaktionsspeichers festgelegt werden, indem die analytische Gültigkeitsdauer auf denselben Wert wie die transaktionale Gültigkeitsdauer festgelegt wird.

Sie können den Analysespeicher für einen Container auf folgende Weise aktivieren:

* Über das Azure-Portal wird die Option für analytische Gültigkeitsdauer (sofern aktiviert) auf den Standardwert „-1“ festgelegt. Sie können diesen Wert in ‚n‘ Sekunden ändern, indem Sie unter „Daten-Explorer“ zu „Containereinstellungen“ navigieren. 
 
* Über das Azure Management SDK, Azure Cosmos DB-SDKs, PowerShell oder die CLI kann die Option für analytische Gültigkeitsdauer aktiviert werden, indem sie entweder auf „-1“ oder „n“ Sekunden festgelegt wird. 

Weitere Informationen finden Sie unter [Konfigurieren der analytischen Gültigkeitsdauer für einen Container](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Dokumenten:

* [Azure Synapse Link für Azure Cosmos DB](synapse-link.md)

* Sehen Sie sich das Lernmodul zum [Entwerfen der Hybridanalysen und Transaktionsverarbeitung mit Azure Synapse Analytics](/learn/modules/design-hybrid-transactional-analytical-processing-using-azure-synapse-analytics/) an

* [Erste Schritte mit Azure Synapse Link für Azure Cosmos DB](configure-synapse-link.md)

* [Häufig gestellte Fragen zu Azure Synapse Link für Azure Cosmos DB](synapse-link-frequently-asked-questions.yml)

* [Anwendungsfälle für Azure Synapse Link für Azure Cosmos DB](synapse-link-use-cases.md)

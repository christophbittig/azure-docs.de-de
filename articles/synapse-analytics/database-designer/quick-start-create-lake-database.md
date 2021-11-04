---
title: Schnellstart für Azure-Synapse-Lake-Datenbanken and Datenbank-Vorlagen
description: Schnellstart Wie verwende ich die Synapse-Lake-Datenbanken und die Datenbank-Vorlagen
author: gsaurer
ms.author: gesaur
ms.service: synapse-analytics
ms.subservice: database-editor
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 4db8799afdaf4a69278ce40c76baf05ab0eb1eee
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467186"
---
# <a name="quickstart-create-a-new-lake-database-leveraging-database-templates"></a>Schnellstart: Erstellen Sie eine neue Lake-Datenbank anhand von Datenbank-Vorlagen

Dieser Schnellstart gibt Ihnen eine kurze Zusammenfassung von einem durchlaufenden (End-2-End-)Szenarium darüber, wie Sie die Datenbankvorlagen anwenden können, um eine Lake-Datenbank zu erstellen, die Daten für Ihr neues Objekt ausrichten und die integrierte Erfahrung nutzen, um die Daten zu analysieren. 

## <a name="prerequisites"></a>Voraussetzungen
- Sie benötigen zumindest eine Synapse Nutzerrollenzugangsberechtigung, um auf eine Lake-Datenbank-Vorlage aus der Gallerie zugreifen zu können.
- Man benötigt eine Zugangsberechtigung zum Synapse-Administrator oder Synapse-Verteiler auf dem Synapse-Arbeitsbereich, um eine Lake-Datenbank zu erstellen.
- Bei Data Lake benötigt man eine Zugangsberechtigung für den Blob-Storage-Daten-Verteiler.

## <a name="create-a-lake-database-from-database-templates"></a>Eine Lake-Datenbank aus den Datenbank-Vorlagen erstellen

Verwenden Sie die Funktion neue Datenbank-Vorlagen (Vorschau), um eine Lake-Datenbank zu erstellen, die Sie verwenden können, um Ihr Datenobjekt für die neue Datenbank zu konfigurieren. 

Für unser Szenarium verwenden wir die Vorlage für die Einzelhandelsdatenbank und wählen die folgenden Punkte aus: 
 - **Einzelhandelsprodukt** - Ein Produkt ist alles, was auf dem Markt angeboten werden kann, das einen Bedarf durch  mögliche Kunden decken kann. Dieses Produkt ist die Summe aller physischer, psychologischer, symbolischer und dienstleistungsorientierter Eigenschaften, die damit verbunden sind.
 - **Transaktion** - Die unterste Stufe von ausführbarer Arbeit oder Kundenaktivität.
Eine Transaktion besteht aus einem oder mehreren diskreten Ereignissen.
 - **TransaktionEinzelposten** - Die Komponenten einer Transaktion aufgeschlüsselt nach Produkt und Menge, eines pro Einzelposten.
 - **Partei** - Eine Partei ist ein Individuum, eine Organisation, eine juristische Person, eine soziale Organisation oder ein Geschäftsbereich, der von Interesse für das Geschäft ist.
 - **Kunde** - Ein Kunde ist ein Individuum oder eine juristische Person, das oder die ein Produkt hat oder eine Dienstleistung in Anspruch nimmt oder erworben hat.
 - **Kanal** - Ein Kanal ist ein Weg, durch den Produkte oder Dienstleistungen verkauft und / oder vertrieben werden.
 - 
Der einfachste Weg, um sie aufzufinden, ist über das Suchfenster überhalb der verschiedenen Geschäftsbereiche, die die Verzeichnisse enthalten. 
 
![Beispiel für eine Datenbank-Vorlage](./media/quick-start-create-lake-database/model-example.png)

 
## <a name="configure-lake-database"></a>Lake-Datenbank konfigurieren
 
Nachdem Sie die Datenbank erstellt haben, vergewissern Sie sich, dass das Speicherkonto und der Dateipfad auf den Speicherort lauten, wo Sie die Daten speichern möchten. Der Pfad ist auf das erste Speicherkonto innerhalb Synapse Analytics voreingestellt, kann aber Ihren Bedürfnissen angepasst werden. 
  
 ![Beispiel für eine Lake-Datenbank](./media/quick-start-create-lake-database/lake-database-example.png)
 
Um Ihren Entwurf zu speichern und ihn innerhalb von Synapse zugänglich zu machen, veröffentlichen Sie alle Änderungen. Dieser Schritt schließt das Erstellen der Lake-Datenbank ab und stellt sie für alle Komponenten innerhalb und außerhalb von Synapse Analytics zur Verfügung. 

## <a name="ingest-data-to-lake-database"></a>Daten in die Lake-Datenbank aufnehmen

Um Daten in die Lake-Datenbank aufzunehmen, können Sie [Pipelines](../data-integration/data-integration-data-lake.md) mit unverschlüsselten Datenflusszuordnungen (Data Flow Mappings) errichten, die über einen **Workspace DB** Connector verfügen, um die Daten direkt in das Datenbank-Verzeichnis zu laden. Sie können auch die interaktiven Spark-Notebooks verwenden, um Daten in die Lake-Datenbank-Verzeichnisse aufzunehmen:

```Spark
%%sql
INSERT INTO `retail_mil`.`customer` VALUES (1,'2021-02-18',1022,557,101,'Tailspin Toys (Head Office)','Waldemar Fisar',90410,466);
```

## <a name="query-the-data"></a>Abfragen der Daten

Nachdem die Lake-Datenbank erstellt wurde, gibt es verschiedene Möglichkeiten, um die Daten abzufragen. Aktuell unterstützen wir SQL-Ondemand innerhalb Synapse, das das neu erstellte Datenbank-Format automatisch erkennt und die Daten dadurch bereitstellt. 

```sql
SELECT TOP (100) [ProductId]
,[ProductName]
,[ProductDescription]
,[ProductInternalName]
,[ItemSku]
,[PrimaryBrandId]
FROM [Retail_mil].[dbo].[RetailProduct]
```

Eine andere Möglichkeit, auf die Daten innerhalb von Synapse zuzugreifen, ist, ein neues Spark-Notebook zu öffnen und hier die integrierte Erfahrung zu nutzen:

```spark
df = spark.sql("SELECT * FROM `Retail_mil`.`RetailProduct`")
df.show(10)
```

## <a name="train-machine-learning-models"></a>Trainieren von Machine Learning-Modellen

Sie können die Lake-Datenbank nutzen, um Konzepte von maschinellem Lernen zu trainieren und die Daten auszuwerten. Für mehr Einzelheiten siehe [Konzepte von maschinellem Lernen trainieren](../machine-learning/tutorial-automl.md) 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Möglichkeiten des Database Designers, indem Sie den Link unten verwenden.
 - [Datenbank-Vorlagen-Konzept](concepts-database-templates.md)
 - [Lake-Datenbank-Konzept](concepts-lake-database.md)
 - [Wie Sie eine Lake-Datenbank erstellen](create-empty-lake-database.md)

---
title: Anwenden der SQL-Transformation
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Komponente "SQL-Transformation anwenden" in Azure Machine Learning verwenden, um eine SQLite-Abfrage auf Eingabedatensätzen auszuführen und die Daten zu transformieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/11/2021
ms.openlocfilehash: 31bbdc8a7b274f020c814c5f39ca1ce4e426ae7e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566515"
---
# <a name="apply-sql-transformation"></a>Anwenden der SQL-Transformation

Dieser Artikel beschreibt eine Komponente von Azure Machine Learning Designer.

Mit der Komponente SQL-Transformation anwenden können Sie:
  
-   Erstellen von Tabellen für Ergebnisse und Speichern der Datasets in einer portierbaren Datenbank  
  
-   Ausführen von benutzerdefinierten Transformationen für Datentypen oder Erstellen von Aggregaten  
  
-   Ausführen von SQL-Abfrageanweisungen, um Daten zu filtern oder zu ändern und die Abfrageergebnisse als Datentabelle zurückzugeben  

> [!IMPORTANT]
> Die in dieser Komponente verwendete SQL-Engine ist **SQLite**. Weitere Informationen zur SQLite-Syntax finden Sie unter [SQL As Understood By SQLite](https://www.sqlite.org/index.html).
> Diese Komponente überträgt Daten in SQLite, das sich in der Speicher-DB befindet. Daher benötigt die Komponentenausführung viel mehr Speicher und kann auf einen `Out of memory`-Fehler stoßen. Stellen Sie sicher, dass Ihr Computer über genügend RAM verfügt.

## <a name="how-to-configure-apply-sql-transformation"></a>Konfigurieren von „Apply SQL Transformation“  

Die Komponente kann bis zu drei Datensätze als Eingaben verarbeiten. Wenn Sie auf die Datasets verweisen, die mit den einzelnen Eingabeports verbunden sind, müssen Sie die Namen `t1`, `t2` und `t3` verwenden. Die Tabellennummer gibt den Index des Eingabeports an.  

Das folgende Codebeispiel zeigt, wie zwei Tabellen verknüpft werden können. t1 und t2 sind zwei Datasets, die mit dem linken und mittleren Eingabeport von **Anwenden der SQL-Transformation** verbunden sind:

```sql
SELECT t1.*
    , t3.Average_Rating
FROM t1 join
    (SELECT placeID
        , AVG(rating) AS Average_Rating
    FROM t2
    GROUP BY placeID
    ) as t3
on t1.placeID = t3.placeID
```
  
Der verbleibende Parameter ist eine SQL-Abfrage, für die die SQLite-Syntax verwendet wird. Wenn Sie mehrere Zeilen in das Textfeld **SQL-Skript** eingeben, verwenden Sie ein Semikolon, um jede Anweisung zu beenden. Andernfalls werden Zeilenumbrüche in Leerzeichen konvertiert.  

Diese Komponente unterstützt alle Standardanweisungen der SQLite-Syntax. Eine Liste der nicht unterstützten-Anweisungen finden Sie im Abschnitt [Technische Hinweise](#technical-notes).

##  <a name="technical-notes"></a>Technische Hinweise  

Dieser Abschnitt enthält Implementierungsdetails, Tipps und Antworten auf häufig gestellte Fragen.

-   An Port 1 ist immer eine Eingabe erforderlich.  
  
-   Enthält ein Spaltenbezeichner (Spalten-ID) ein Leerzeichen oder andere Sonderzeichen, müssen Sie den Bezeichner in eckige Klammern oder doppelte Anführungszeichen einschließen, wenn Sie in einer `SELECT`- oder `WHERE`-Klausel auf die Spalte verweisen.  

-   Wenn Sie **Metadaten bearbeiten** verwendet haben, um die Spaltenmetadaten (Kategorie oder Felder) vor **SQL-Transformation anwenden** anzugeben, enthalten die Ausgaben von **SQL-Transformation anwenden** diese Attribute nicht. Sie müssen **Metadaten bearbeiten** verwenden, um die Spalte nach der Ausführung von **SQL-Transformation anwenden** zu bearbeiten.
  
### <a name="unsupported-statements"></a>Nicht unterstützte Anweisungen  

SQLite unterstützt zwar einen Großteil des ANSI SQL-Standards, umfasst aber nicht viele Funktionen, die von kommerziellen relationalen Datenbanksystemen unterstützt werden. Weitere Informationen finden Sie unter [SQL as Understood by SQLite](http://www.sqlite.org/lang.html). Beachten Sie beim Erstellen von SQL-Anweisungen außerdem die folgenden Einschränkungen:  
  
- SQLite verwendet dynamische Typzuweisung für Werte, anstatt jeder Spalte einen Typ zuzuweisen, wie dies in den meisten relationalen Datenbanksystemen geschieht. SQLite ist schwach typisiert und ermöglicht implizite Typkonvertierung.  
  
- `LEFT OUTER JOIN` ist implementiert, `RIGHT OUTER JOIN` und `FULL OUTER JOIN` sind dagegen nicht implementiert.  

- Sie können `RENAME TABLE`- und `ADD COLUMN`-Anweisungen mit dem `ALTER TABLE`-Befehl verwenden, andere Klauseln werden jedoch nicht unterstützt, einschließlich `DROP COLUMN`, `ALTER COLUMN` und `ADD CONSTRAINT`.  
  
- Sie können eine SICHT in SQLite erstellen, aber danach sind Sichten schreibgeschützt. Sie können keine `DELETE`-, `INSERT`- oder `UPDATE`-Anweisung für eine Sicht ausführen. Sie können jedoch einen Trigger erstellen, der beim Ausführen einer `DELETE`-, `INSERT`- oder `UPDATE`-Anweisung für eine Sicht ausgelöst wird, und Sie können in dem Trigger weitere Vorgänge ausführen.  
  

Zusätzlich zur Liste der nicht unterstützten Funktionen, die auf der offiziellen SQLite-Website zu finden ist, wird im folgenden Wiki eine Liste weiterer nicht unterstützter Features bereitgestellt: [SQLite - Unsupported SQL](http://www2.sqlite.org/cvstrac/wiki?p=UnsupportedSql)  
    
## <a name="next-steps"></a>Nächste Schritte

Siehe die [Sammlung der für Azure Machine Learning verfügbaren Komponenten](component-reference.md). 

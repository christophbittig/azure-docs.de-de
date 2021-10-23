---
title: 'Azure Monitor-Arbeitsmappen: Transformieren von JSON-Daten mit JSONPath'
description: Erfahren Sie, wie Sie JSONPath in Azure Monitor-Arbeitsmappen verwenden, um die Ergebnisse von JSON-Daten, die von einem abgefragten Endpunkt zurückgegeben werden, in das gewünschte Format zu transformieren.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: f43fde52d8b85483a5d10548053fb7ca421f93c1
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132377"
---
# <a name="how-to-use-jsonpath-to-transform-json-data-in-workbooks"></a>Verwenden von JSONPath zum Transformieren von JSON-Daten in Arbeitsmappen

Mit Arbeitsmappen können Daten aus vielen Quellen abgefragt werden. Einige Endpunkte, z. B. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) oder ein benutzerdefinierter Endpunkt, können Ergebnisse in JSON zurückgeben. Wenn die vom abgefragten Endpunkt zurückgegebenen JSON-Daten nicht in einem gewünschten Format konfiguriert sind, können die Ergebnisse mithilfe von JSONPath transformiert werden.

JSONPath ist eine Abfragesprache für JSON, die XPath für XML ähnelt. Wie XPath ermöglicht auch JSONPath das Extrahieren und Filtern von Daten aus der JSON-Struktur.

Mithilfe der JSONPath-Transformation können Arbeitsmappenautoren JSON in eine Tabellenstruktur konvertieren. Die Tabelle kann anschließend für [Visualisierungen von Arbeitsmappen](./workbooks-overview.md#visualizations) verwendet werden.

## <a name="using-jsonpath"></a>Verwenden von JSONPath

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement *Bearbeiten* klicken.
2. Fügen Sie über den Link *Hinzufügen* > *Abfrage hinzufügen* der Arbeitsmappe ein Abfragesteuerelement hinzu.
3. Wählen Sie *JSON* als Datenquelle aus.
4. Geben Sie den folgenden JSON-Ausschnitt mithilfe des JSON-Editors ein:
    ```json
    { "store": {
        "books": [ 
          { "category": "reference",
            "author": "Nigel Rees",
            "title": "Sayings of the Century",
            "price": 8.95
          },
          { "category": "fiction",
            "author": "Evelyn Waugh",
            "title": "Sword of Honour",
            "price": 12.99
          },
          { "category": "fiction",
            "author": "Herman Melville",
            "title": "Moby Dick",
            "isbn": "0-553-21311-3",
            "price": 8.99
          },
          { "category": "fiction",
            "author": "J. R. R. Tolkien",
            "title": "The Lord of the Rings",
            "isbn": "0-395-19395-8",
            "price": 22.99
          }
        ],
        "bicycle": {
          "color": "red",
          "price": 19.95
        }
      }
    }
    ```  

Angenommen, Sie erhalten das oben gezeigte JSON-Objekt zur Darstellung des Bestands in einem Laden. Ihre Aufgabe ist es, eine Tabelle mit den verfügbaren Büchern im Laden zu erstellen, indem Sie die jeweiligen Titel, Autoren und Preise auflisten.

1. Wählen Sie die Registerkarte *Ergebniseinstellungen* aus, und ändern Sie das Ergebnisformat in *JSON-Pfad*.
2. Verwenden Sie die folgenden Einstellungen für den JSON-Pfad:

    JSON-Pfadtabelle: `$.store.books`. Dieses Feld stellt den Pfad des Stamms der Tabelle dar. In diesem Fall ist der Buchbestand des Ladens von Bedeutung. Der Tabellenpfad filtert den JSON-Code auf die Buchinformationen.

   | Spalten-IDs | JSON-Spaltenpfad |
   |:-----------|:-----------------|
   | Titel      | `$.title`        |
   | Autor     | `$.author`       |
   | Preis      | `$.price`        |

    Spalten-IDs sind die Spaltenüberschriften. Felder für den JSON-Spaltenpfad stellen den Pfad vom Stamm der Tabelle zum Spaltenwert dar.

1. Wenden Sie die oben angegebenen Einstellungen an, indem Sie auf *Abfrage ausführen* klicken.

![ Bearbeiten des Abfrageelements mit JSON-Datenquelle und Ergebnisformat des JSON-Pfads](./media/workbooks-jsonpath/query-jsonpath.png)

## <a name="next-steps"></a>Nächste Schritte
- [Übersicht über Arbeitsmappen](./workbooks-overview.md)
- [Gruppen in Azure Monitor-Arbeitsmappen](workbooks-groups.md)
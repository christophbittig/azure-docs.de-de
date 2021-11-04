---
title: Teilaktualisierung von Dokumenten in Azure Cosmos DB
description: Hier erfahren Sie mehr über die Teilaktualisierung von Dokumenten in Azure Cosmos DB.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: abhishgu
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7f5925ffa70977d13a2eefb25dc48898ac513f6e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434083"
---
# <a name="partial-document-update-in-azure-cosmos-db"></a>Teilaktualisierung von Dokumenten in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Das Azure Cosmos DB-Feature für die Teilaktualisierung von Dokumenten (auch „Patch-API“) bietet eine praktische Möglichkeit, Dokumente in einem Container zu bearbeiten. Um ein Dokument zu aktualisieren, muss der Client es derzeit lesen, (falls erforderlich) Überprüfungen der optimistischen Gleichzeitigkeitserkennung ausführen, das Dokument lokal aktualisieren und es dann über das Netzwerk als API-Aufruf zum Ersetzen des gesamten Dokuments senden. 

Das Feature für die Teilaktualisierung von Dokumenten verbessert diesen Vorgang erheblich. Der Client kann nur die in einem Dokument geänderten Eigenschaften/Felder senden, ohne einen Vorgang zum vollständigen Ersetzen des Dokuments auszuführen. Zu den wichtigsten Vorteilen dieses Features zählen:

- **Verbesserte Entwicklerproduktivität:** Die praktische API ist benutzerfreundlich und bietet die Möglichkeit, das Dokument bedingt zu aktualisieren. 
- **Leistungsverbesserungen:** Das Feature vermeidet zusätzliche CPU-Zyklen auf Clientseite, reduziert die End-to-End-Latenz und erhöht die Netzwerkbandbreite.
- **Schreibvorgänge in mehreren Regionen:** Das Feature unterstützt die automatische und transparente Konfliktlösung durch die Teilaktualisierung von diskreten Pfaden innerhalb desselben Dokuments.
 
## <a name="supported-operations"></a>Unterstützte Vorgänge

In der folgenden Tabelle sind die Vorgänge zusammengefasst, die von diesem Feature unterstützt werden.

> [!NOTE]
> Der *Zielpfad* verweist auf einen Speicherort innerhalb des JSON-Dokuments.

| **Vorgangstyp** | **Beschreibung** |
| ------------ | -------- |
| **Add (Hinzufügen)**      | `Add` führt je nach Zielpfad eine der folgenden Schritte aus: <br/><ul><li>Wenn der Zielpfad ein Element angibt, das nicht vorhanden ist, wird das Element hinzugefügt.</li><li>Wenn der Zielpfad ein Element angibt, das bereits vorhanden ist, wird sein Wert ersetzt.</li><li>Wenn der Zielpfad einen gültigen Arrayindex darstellt, wird am angegebenen Index ein neues Element in das Array eingefügt. Vorhandene Elemente werden nach rechts verschoben.</li><li>Wenn der angegebene Index gleich der Länge des Arrays ist, fügt er ein Element an das Array an. Anstatt einen Index anzugeben, können Sie auch das Zeichen `-` verwenden. Es führt auch dazu, dass das Element an das Array angefügt wird.</li></ul> <br/> **Hinweis:** Wird ein Index angegeben, der größer als die Arraylänge ist, führt dies zu einem Fehler.|
| **Set**      | Der `Set`-Vorgang ähnelt `Add`, außer im Fall des Datentyps „Array“. Wenn der Zielpfad ein gültiger Arrayindex ist, wird das vorhandene Element an diesem Index aktualisiert.| 
| **Replace**      | Der `Replace`-Vorgang ähnelt `Set` mit der Ausnahme, dass er einer _strikten_ Semantik zum ausschließlichen Ersetzen folgt. Falls der Zielpfad ein Element oder ein Array angibt, das nicht vorhanden ist, führt dies zu einem Fehler.  | 
| **Remove**     | `Remove` führt je nach Zielpfad eine der folgenden Schritte aus: <br/><ul><li>Falls der Zielpfad ein Element angibt, das nicht vorhanden ist, führt dies zu einem Fehler. </li><li> Wenn der Zielpfad ein Element angibt, das bereits vorhanden ist, wird es entfernt. </li><li> Wenn der Zielpfad ein Arrayindex ist, wird er gelöscht, und alle Elemente über dem angegebenen Index werden um eine Position nach links verschoben.</li></ul> <br/> **Hinweis:** Wird ein Index angegeben, der gleich oder größer als die Arraylänge ist, führt dies zu einem Fehler.  |
| **Increment**     | Dieser Operator erhöht ein Feld um den angegebenen Wert. Er kann sowohl positive als auch negative Werte annehmen. Wenn das Feld nicht vorhanden ist, wird das Feld erstellt und auf den angegebenen Wert festgelegt. |

## <a name="supported-modes"></a>Unterstützte Modi

Das Feature für die Teilaktualisierung von Dokumenten unterstützt die folgenden Betriebsmodi. Im Dokument zu den [ersten Schritten](partial-document-update-getting-started.md) finden Sie Codebeispiele.

* **Patches von einzelnen Dokumenten:** Sie können ein einzelnes Dokument basierend auf seiner ID und seinem Partitionsschlüssel patchen. Es ist möglich, mehrere Patchvorgänge für ein einzelnes Dokument auszuführen. Der [Höchstwert liegt bei 10 Vorgängen](partial-document-update-faq.yml#is-there-a-limit-to-the-number-of-partial-document-update-operations-).

* **Patches von mehreren Dokumenten:** Mehrere Dokumente innerhalb desselben Partitionsschlüssels können [im Rahmen einer Transaktion](transactional-batch.md) gepatcht werden. Diese Transaktion wird nur dann committet, wenn alle Vorgänge in der beschriebenen Reihenfolge erfolgreich ausgeführt werden. Schlägt ein Vorgang fehl, wird für die gesamte Transaktion ein Rollback ausgeführt.

* **Bedingte Aktualisierung:** Für die oben genannten Modi ist es auch möglich, ein SQL-ähnliches Filterprädikat hinzuzufügen (z. B. *from c where c.taskNum = 3*), damit der Vorgang fehlschlägt, wenn die im Prädikat angegebene Vorbedingung nicht erfüllt wird. 

* Sie können auch die Massen-APIs unterstützter SDKs verwenden, um mindestens einen Patchvorgang für mehrere Dokumente auszuführen.


## <a name="similarities-and-differences"></a>Ähnlichkeiten und Unterschiede

### <a name="add-vs-set"></a>„Add“ im Vergleich zu „Set“

Der `Set`-Vorgang ähnelt `Add` für alle Datentypen mit Ausnahme von `Array`. Ein `Add`-Vorgang an einem beliebigen (gültigen) Index führt dazu, dass am angegebenen Index ein Element hinzugefügt wird und dass alle vorhandenen Elemente im Array nach rechts verlagert werden. Dies steht im Gegensatz zu einem `Set`-Vorgang, bei dem das vorhandene Element am angegebenen Index aktualisiert wird. 

### <a name="add-vs-replace"></a>„Add“ im Vergleich zu „Replace“

Ein `Add`-Vorgang fügt eine Eigenschaft hinzu, wenn sie noch nicht vorhanden ist (einschließlich des Datentyps `Array`). `Replace`-Vorgänge schlagen fehl, wenn die Eigenschaft nicht vorhanden ist (gilt auch für den Datentyp `Array`).

### <a name="set-vs-replace"></a>„Set“ im Vergleich zu „Replace“

Ein `Set`-Vorgang fügt eine Eigenschaft hinzu, wenn sie noch nicht vorhanden ist (außer bei `Array`-Datentypen). `Replace`-Vorgänge schlagen fehl, wenn die Eigenschaft nicht vorhanden ist (gilt auch für den Datentyp `Array`).

> [!NOTE]
> `Replace` ist ein guter Kandidat, bei dem Benutzer*innen erwarten, dass einige der Eigenschaften immer vorhanden sind, und ermöglicht es Ihnen, dies durchzusetzen/zu erzwingen.

## <a name="partial-document-update-specification"></a>Spezifikation der Teilaktualisierung von Dokumenten

Die clientseitige Komponente der Funktion für die Teilaktualisierung von Dokumenten wird auf der REST-API implementiert. Nachstehend finden Sie ein Beispiel dafür, wie ein `Conditional Add`-Vorgang von Azure Cosmos DB modelliert wird:

```bash
PATCH /dbs/{db}/colls/{coll}/documents/{doc}
HTTP/1.1
Content-Type:application/json-patch+json

{
   "condition":"from c where (c.TotalDue = 0 OR NOT IS_DEFINED(c.TotalDue))", 
   "operations":[ 
      { 
         "op":"add", 
         "path":"amount", 
         "value":80000 
      }
   ]
} 
```

> [!NOTE]
> In diesem Fall wird der Wert im Pfad `amount` *nur dann* auf `80000` (der Vorgang) festgelegt, wenn der Wert von `TotalDue` 0 (null) oder nicht vorhanden ist (die Bedingung).

## <a name="document-level-vs-path-level-conflict-resolution"></a>Konfliktlösung auf Dokument- und Pfadebene im Vergleich  

Wenn Ihr Azure Cosmos DB-Konto mit mehreren Schreibregionen konfiguriert ist, gelten [Konflikt- und Konfliktlösungsrichtlinien](conflict-resolution-policies.md) auf Dokumentebene, bei denen das Prinzip „Letzter Schreibvorgang gewinnt“ (Last Write Wins, `LWW`) die Standardrichtlinie für die Konfliktlösung darstellt. Bei der Teilaktualisierung von Dokumenten erkennen und lösen Patchvorgänge in mehreren Regionen Konflikte auf einer tieferen Pfadebene.

Dies lässt sich anhand eines Beispiels besser verdeutlichen.

Angenommen, Sie verfügen über das folgende Dokument in Azure Cosmos DB:

```json
{  
   "id":1, 
   "name":"John Doe", 
   "email":"jdoe@contoso.com", 
   "phone":[  
      "12345",
      "67890"
   ], 
   "level":"gold"
} 
```

Die folgenden Patchvorgänge werden gleichzeitig von verschiedenen Clients in verschiedenen Regionen ausgegeben:

- `Set` attribute `/level` to platinum  
- `Remove` 67890 from `/phone`

:::image type="content" source="./media/partial-document-update/patch-multi-region-conflict-resolution.png" alt-text="Abbildung, die die Konfliktlösung bei gleichzeitigen Teilaktualisierungen in mehreren Regionen zeigt" border="false" lightbox="./media/partial-document-update/patch-multi-region-conflict-resolution.png":::

Da Patchanforderungen an innerhalb des Dokuments nicht in Konflikt stehende Pfade gestellt wurden, werden diese Konflikte automatisch und transparent aufgelöst (im Gegensatz zum LWW-Prinzip auf Dokumentebene).    

Dem Client sieht nach der Konfliktlösung das folgende Dokument:

```json
{  
   "id":1, 
   "name":"John Doe", 
   "email":"jdoe@contoso.com", 
   "phone":[  
      "12345"
   ], 
   "level":"platinum",
} 
```

> [!NOTE]
> Falls eine Eigenschaft eines Dokuments gleichzeitig in mehreren Regionen gepatcht wird, gelten die regulären [Konfliktlösungsrichtlinien](conflict-resolution-policies.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie mit der Teilaktualisierung von Dokumenten in .NET, Java und Node [beginnen](partial-document-update-getting-started.md).
- [Häufig gestellte Fragen](partial-document-update-faq.yml) zur Teilaktualisierung von Dokumenten

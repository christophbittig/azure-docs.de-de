---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/15/2021
ms.author: glenga
ms.openlocfilehash: 4f2aa82388882c192213b168faedd4f61069ae64
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829538"
---
In einem C#-Projekt werden die Bindungen als Bindungsattribute der Funktionsmethode definiert. Bestimmte Definitionen hängen davon ab, ob Ihre App In-Process (C#-Klassenbibliothek) oder in einem isolierten Prozess ausgeführt wird.  

# <a name="in-process"></a>[In-Process](#tab/in-process)

Öffnen Sie die Projektdatei *HttpExample.cs*, und fügen Sie der Definition der Methode `Run` den folgenden Parameter hinzu:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

Der Parameter `msg` ist ein `ICollector<T>`-Typ und stellt eine Sammlung von Nachrichten dar, die in eine Ausgabebindung geschrieben werden, wenn die Funktion abgeschlossen wird. In diesem Fall ist die Ausgabe eine Speicherwarteschlange mit dem Namen `outqueue`. Die Verbindungszeichenfolge für das Storage-Konto wird durch `StorageAccountAttribute` festgelegt. Dieses Attribut gibt die Einstellung an, die die Verbindungszeichenfolge des Storage-Kontos enthält, und kann auf Klassen-, Methoden- oder Parameterebene angewandt werden. In diesem Fall können Sie `StorageAccountAttribute` weglassen, da Sie bereits das Standardspeicherkonto verwenden.

Die Run-Methodendefinition sollte nun wie folgt aussehen:  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::

# <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)

Öffnen Sie die Projektdatei *HttpExample.cs*, und fügen Sie die folgende `MultiResponse`-Klasse hinzu:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-isolated/HttpExample.cs" range="33-38":::

Mit der `MultiResponse`-Klasse können Sie sowohl in eine Speicherwarteschlange mit dem Namen `outqueue` als auch in eine HTTP-Erfolgsmeldung schreiben. Da das `QueueOutput`-Attribut auf ein Zeichenfolgenarray angewendet wird, können mehrere Nachrichten an die Warteschlange gesendet werden. 

Die Verbindungszeichenfolge für das Storage-Konto wird durch die `Connection`-Eigenschaft festgelegt. In diesem Fall können Sie `Connection` weglassen, da Sie bereits das Standardspeicherkonto verwenden.

---
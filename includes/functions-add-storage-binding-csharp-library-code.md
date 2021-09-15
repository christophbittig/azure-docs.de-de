---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/15/2021
ms.author: glenga
ms.openlocfilehash: 7537ad41aaac6fab1eb591300b561bba4167d85d
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829539"
---
# <a name="in-process"></a>[In-Process](#tab/in-process)

Fügen Sie Code hinzu, der das Ausgabebindungsobjekt `msg` verwendet, um eine Warteschlangennachricht zu erstellen. Fügen Sie diesen Code vor der Rückgabe der Methode hinzu.

:::code language="csharp" range="28-32" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" :::

Die Funktion sollte nun wie folgt aussehen:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-36":::

# <a name="isolated-process"></a>[Isolierter Prozess](#tab/isolated-process)

Ersetzen Sie die vorhandene Klasse `HttpExample` durch den folgenden Code:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-isolated/HttpExample.cs" range="11-32":::

---
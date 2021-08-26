---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 9c3dd4ecede4e8087b2ec9ad470d5ab415ea6e35
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121800911"
---
Aktualisieren Sie *HttpExample\\\_\_init\_\_.py* gemäß dem folgenden Code, und fügen Sie den Parameter `msg` der Funktionsdefinition und `msg.set(name)` unter der `if name:`-Anweisung hinzu.

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Der Parameter `msg` ist eine Instanz der [`azure.functions.Out class`](/python/api/azure-functions/azure.functions.out). Die zugehörige `set`-Methode schreibt eine Zeichenfolgennachricht in die Warteschlange. In diesem Fall ist dies der Name, der in der URL-Abfragezeichenfolge an die Funktion übergeben wird.

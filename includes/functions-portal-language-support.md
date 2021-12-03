---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/23/2021
ms.author: glenga
ms.openlocfilehash: 8d580bbfd87f1d1df9d36be95a98c65d80736806
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224303"
---
## <a name="language-support-details"></a>Details zur Sprachunterstützung 

Die folgende Tabelle zeigt, welche von Functions unterstützten Sprachen unter Linux oder Windows ausgeführt werden können. Außerdem wird angegeben, ob Ihre Sprache die Bearbeitung im Azure-Portal unterstützt. Die Sprache basiert auf der Option **Laufzeitstapel**, die Sie beim [Erstellen Ihrer Funktions-App im Azure-Portal](../articles/azure-functions/functions-create-function-app-portal.md#create-a-function-app) auswählen. Diese entspricht der Option `--worker-runtime` bei Verwendung des Befehls `func init` in den Azure Functions Core Tools. 

| Sprache | Laufzeitstapel | Linux | Windows | Bei Bearbeitung im Portal<sup>1</sup> |
|:--- |:-- |:--|:--- |:--- |
| [C#-Klassenbibliothek](../articles/azure-functions/functions-dotnet-class-library.md)<sup>2</sup> |.NET|✓ |✓ | | 
| [C#-Skript](../articles/azure-functions/functions-reference-csharp.md) | .NET | ✓ |✓ |✓ |
| [JavaScript](../articles/azure-functions/functions-reference-node.md) | Node.js |✓ |✓ | ✓ |
| [Python](../articles/azure-functions/functions-reference-python.md) | Python |✓ | | |
| [Java](../articles/azure-functions/functions-reference-java.md) | Java |✓ |✓ | |
| [PowerShell](../articles/azure-functions/functions-reference-powershell.md) |PowerShell Core | |✓ |✓ |
| [TypeScript](../articles/azure-functions/functions-reference-node.md) | Node.js |✓ |✓ |  |
| [Go/Rust/other](../articles/azure-functions/functions-custom-handlers.md) | Benutzerdefinierte Handler |✓ |✓ | |

<sup>1</sup> Bei Ausführung unter Linux wird die Bearbeitung im Portal nur in einem [dedizierten Plan (App Service)](../articles/azure-functions/dedicated-plan.md) unterstützt.   
<sup>2</sup> Im Portal können Sie derzeit keine Funktions-Apps erstellen, die unter .NET 5.0 ausgeführt werden. Weitere Informationen finden Sie unter [Entwickeln und Veröffentlichen von .NET 5-Funktionen mit Azure Functions](../articles/azure-functions/dotnet-isolated-process-guide.md). 

Weitere Informationen finden Sie unter [Betriebssystem-/Laufzeitunterstützung](../articles/azure-functions/functions-scale.md#operating-systemruntime). 

Wenn die Bearbeitung im Portal nicht verfügbar ist, müssen Sie [stattdessen Ihre Funktionen lokal entwickeln](../articles/azure-functions/functions-develop-local.md#local-development-environments).